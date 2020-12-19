# http header

- allow:服务器支持哪些请求方法，如get，post等
- content-encoding，文档的编码方法，
- content-length：内容长度
- content-type:表示后面的文档属于什么mime类型，servelet默认text/plain，但是通常需要显式的指定为text/html
- date：当前的gmt时间
- expires：应该在什么时候认为文档已经过期，从而不再缓存它
- last-modified：文档的最后改动时间，
- location：表示客户应该到哪里去提取文档
- refresh：表示浏览器应该在多少时间以后刷新文档
- server：服务器名字
- set-cookie：设置和页面相关联的cookie
- www-authenticate：客户应该在authorization头中提供什么类型的授权信息。