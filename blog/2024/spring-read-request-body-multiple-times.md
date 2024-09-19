# 起因
在某次项目中，我需要在 `Filter` 中获取 `request` 的 `body` 部分的内容以检验数据是否符合要求。
但当我在 `Filter` 添加通过 `request.getReader()` 获取 `body` 的代码后，进行测试时，抛出了异常，异常
信息显式 `getReader() has already been called for this request stack`。

# 问题分析
对于一个流而言，其通常只能被读取一次，当我们在 `Filter` 中进行第一次读取后，后续到 `Controller` 部分
时，由于有 `@RequestBody` 注释的对象，此时会再次读取 `body` 部分，这时就会抛出异常。

# 解决方案
解决方案也很简单，我们只需要自定义一个 `CachedBodyHttpServletReqeust` 继承 `HttpServletRequestWrapper`，
在其中缓存 `body` 部分的内容，然后在 `Filter` 中获取 `body` 时，直接从缓存中获取即可：

```java
private class CachedBodyHttpServletRequest extends HttpServletRequestWrapper {
    private class CachedBodyServletInputStream extends ServletInputStream {
        private final InputStream cacheBodyInputStream;

        public CachedBodyServletInputStream(byte[] cacheBody) {
            this.cacheBodyInputStream = new ByteArrayInputStream(cacheBody);
        }

        @Override
        public boolean isFinished() {
            try {
                return cacheBodyInputStream.available() == 0;
            } catch (IOException e) {
                return true;
            }
        }

        @Override
        public boolean isReady() {
            return true;
        }

        @Override
        public void setReadListener(ReadListener listener) {
            throw new UnsupportedOperationException();
        }

        @Override
        public int read() throws IOException {
            return cacheBodyInputStream.read();
        }
    }

    private final byte[] cacheBody;

    public CachedBodyHttpServletRequest(HttpServletRequest request) throws IOException {
        super(request);
        InputStream requestInputStream = request.getInputStream();
        this.cacheBody = StreamUtils.copyToByteArray(requestInputStream);
    }

    @Override
    public ServletInputStream getInputStream() {
        return new CachedBodyServletInputStream(this.cacheBody);
    }

    @Override
    public BufferedReader getReader() {
        return new BufferedReader(
                new InputStreamReader(new ByteArrayInputStream(this.cacheBody)));
    }
}
```

这样我们在 `Filter` 中进行处理的时候需要创建一个 `CachedBodyHttpServletRequest` 对象，然后将其传递给
`FilterChain` 的 `doFilter` 方法即可：

```java
@Override
protected void doFilterInternal(
        HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
        throws ServletException, IOException {
    CachedBodyHttpServletRequest cachedRequest = new CachedBodyHttpServletRequest(request);
    // do something
    filterChain.doFilter(cachedRequest, response);
}
```

# 巨人的肩膀
* [Reading Request Body Multiple Times in Java/Spring Boot](https://dev.to/cynavi/reading-request-body-multiple-times-in-javaspring-boot-1j53)
