###### URL编码`%xx`

* URL编码:

  * URL编码并不能解决 客户端,服务端 字符编码不一致问题.

  * URL编码是为了解决 客户端,服务端 字符编码不一致, 服务端使用字符编码解析请求头, 导致数据丢失的问题.

* Tomcat服务器默认使用`ISO8859-1`编码:
  * `ISO8859-1`编码一个字节表示一个字符, 且字节能表示的255个数都有对应字符, 不存在(`UNKNOW`字符).
  * 使用`ISO8859-1`编码解析请求头时,即使编码不一致, 也不会导致数据丢失的问题.