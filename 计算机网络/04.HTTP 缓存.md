## HTTP 缓存

> 强缓存、协商缓存

对于某些重复的请求，每次请求的数据都是相同的，此时可以把这些数据缓存再本地以减少请求，提高性能。

#### 强缓存

强缓存指的是只要浏览器判断缓存没过期，则直接使用浏览器的本地缓存，决定是否使用缓存的主动性在于浏览器这边，状态码为 **200** 。

- **Expires**:	Expires字段是 HTTP 1.0 中定义缓存的字段，它给出了缓存过期的 **绝对** 时间。于此同时，因为客户端的不同他的过期时间也会变得不精确。举例：Expires: Wed, 21 Oct 2022 18:01:00 GMT。

- **Catch-Control**:	Catch-Control 字段是 HTTP 1.1 中定义缓存的字段。其值有多个关键字可选，常用的有：**max-age**，**no-store**，**no-cache** 等。

- <img src="https://blog-assets-1301745442.cos.ap-nanjing.myqcloud.com/assets/image-20220513202226342.png" style="width:500px;margin-left:0px;margin-top:10px" />
  
  - 如上所示，当浏览器发起 HTTP 请求时，会向浏览器缓存进行一次询问，若浏览器缓存没有该资源的缓存数据，那么浏览器便会向服务器发起请求，服务器接收请求后将资源返回给浏览器，浏览器会将资源的响应数据存储到浏览器缓存中，这便是 **强缓存的生成过程**。
  
- <img src="https://blog-assets-1301745442.cos.ap-nanjing.myqcloud.com/assets/image-20220513202410184.png" style="width:500px;margin-left:0px;margin-top:10px" />

  - 图中我们可以看到 **浏览器并没有和服务器进行交互**，而是在发起请求时浏览器缓存告诉浏览器它那有该资源的缓存数据并且还没有过期，于是浏览器直接加载了缓存中的数据资源，这便是 **强缓存的生效流程**。

#### 协商缓存

当我们在浏览器使用开发者工具的时候，你可能会看到过某些请求的响应码是 304，这个是告诉浏览器可以使用本地缓存的资源，通常这种通过服务端告知客户端是否可以使用缓存的方式被称为 **协商缓存**

- **Last-Modified** 与 **If-Modified-Since**:	**Last-Modified** 表示资源最后的修改时间，当浏览器第一次接收到服务器返回资源的 Last-Modified 值后，会把它存储起来，在下次请求时通过携带 **If-Modified-Since** 请求首部让服务器验证其有没有过期。
  - 如果在 If-Modified-Since 字段指定的时间之后 **资源发生了更新 **，那么服务器会将更新的资源发送给浏览器并返回最新的 Last-Modified 值，浏览器收到资源后会更新缓存的 If-Modified-Since 的值。
 - 如果在 If-Modified-Since 字段指定的时间之后 **资源都没有发生更新 **，那么服务器会返回 `304 Not Modified` 表示资源未更新，可以继续使用。

- <img src="https://blog-assets-1301745442.cos.ap-nanjing.myqcloud.com/assets/image-20220513202611640.png" style="width:500px;margin-left:0px;margin-top:10px" />

- **Etag** 与 **If-None-Match**:	Etag 首部值为该资源的唯一标识，当资源变化的时候 Etag 的值也会进行更新，与上面的一组字段类似，Etag 和 If-None-Match 对比是否相同来判断资源是否过期。
  - 如果服务器发现 If-None-Match 值与 Etag 不一致时，说明服务器上的文件已经被更新，那么 **服务器** 会发送更新后的资源给浏览器并返回最新的 **Etag** 值，**浏览器** 收到资源后会更新缓存的 **If-None-Match** 的值。

**总结：**协商缓存是与服务端协商之后，通过协商结果来判断是否使用本地缓存。

#####  协商缓存两种方式的对比

第一种实现方式是基于时间实现的，第二种实现方式是基于一个唯一标识实现的，相对来说后者可以更加准确地判断文件内容是否被修改，避免由于时间篡改导致的不可靠问题。

#### Cache-Control 

##### 验证流程

<img src="https://blog-assets-1301745442.cos.ap-nanjing.myqcloud.com/assets/image-20220513203700964.png" style="width:500px;margin-left:0px;margin-top:10px" />

##### 常用字段

| 字段     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| max-age  | 该指令给出了缓存过期的 **相对** 时间，单位为秒。当其与 **Expires** 同时出现时，**max-age **的优先级更高。 |
| no-store | 缓存不应存储有关客户端请求或服务器响应的任何内容，即 **不使用任何缓存 **。 |
| no-cache | 在发布缓存副本之前，强制要求缓存把请求提交给原始服务器进行验证(**协商缓存验证**)。 |