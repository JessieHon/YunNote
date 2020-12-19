# java调用微信接口

1. **redis缓存中根据key得到的value不为空，但是报错还是过期。**

   - 原因：在即将达到redis缓存失效时间时，可以通过key从redis中得到value，但是依旧为空，

   - 解决办法，二次判断

   - ![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201024174255272.png)

     ```
     @Autowired
     private RedisUtil redisUtil;
     
     @GetMapping("getLiveRooms")//农村小铺项目
     @ApiOperation(value = "获取直播间列表以及直播间信息")
     @ApiImplicitParams({
             @ApiImplicitParam(name = "start", paramType = "query", value = "起始房间", required = true,defaultValue ="0" ),
             @ApiImplicitParam(name = "limit", paramType = "query", value = "每次拉起的房间数量", required = true)
     })
     public AjaxResult getLiveRooms (@RequestParam(value = "start")Integer start,@RequestParam(value = "limit")Integer limit)throws IOException{
         String access_token="";
         HashMap<Object, Object> map = new HashMap<>();
         boolean reAsk=true;/*是否重新请求*/
         if(redisUtil.get("getLiveToken")!=null){
             reAsk=false;
             Object o = redisUtil.get("getLiveToken");
             access_token=(String)o;
             if(access_token==null){
                 reAsk=true;
             }
         }
         if(reAsk==true) {
             /*获取access_token*/
             String url = new StringBuilder().append("https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential")
                     .append("&appid="+ WXConst.appId)
                     .append("&secret="+WXConst.appSecret)
                     .toString();
             CloseableHttpClient client = HttpClients.createDefault();
             HttpGet httpGet = new HttpGet(url);
             CloseableHttpResponse res = client.execute(httpGet);
             if (res.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
                 HttpEntity entity = res.getEntity();
                 String result = EntityUtils.toString(entity, "UTF-8");
                 JSONObject jsonObject = JSON.parseObject(result);
                 access_token = jsonObject.getString("access_token");
                 redisUtil.set("getLiveToken",access_token,7200);
     
             }else {
                 return AjaxResult.success("获取access_token失败！");
             }
         }
     
         String url2 = new StringBuilder().append("https://api.weixin.qq.com/wxa/business/getliveinfo")
                 .append("?access_token="+ access_token)
                 .append("&start="+start)
                 .append("&limit="+limit)
                 .toString();
     
         JSONObject json2=new JSONObject();
     
         json2.put("start",start);
         json2.put("limit",limit);
         Map<String, String> map2=new HashMap<>();
         map2.put("DATA", json2.toString());
         String url="https://api.weixin.qq.com/wxa/business/getliveinfo?access_token="+access_token;
         Object res = httpPost(url, map2);
         map.put("lives",res);
     
     
         return AjaxResult.success(map);
     }
     
     
     public static Object httpPost(String urlStr, Map<String,String> params){
         URL connect;
         StringBuffer data = new StringBuffer();
         try {
             connect = new URL(urlStr);
             HttpURLConnection connection = (HttpURLConnection)connect.openConnection();
             connection.setRequestMethod("POST");
             connection.setDoOutput(true);
             connection.setDoInput(true);
             connection.setUseCaches(false);//post不能使用缓存
             connection.setInstanceFollowRedirects(true);
             connection.setRequestProperty("accept", "*/*");
             connection.setRequestProperty("connection", "Keep-Alive");
             connection.setRequestProperty("user-agent", "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1;SV1)");
             OutputStreamWriter paramout = new OutputStreamWriter(
                     connection.getOutputStream(),"UTF-8");
             String paramsStr = "";   //拼接Post 请求的参数
             for(String param : params.keySet()){
                 paramsStr += "&"+params.get(param);
             }
             if(!paramsStr.isEmpty()){
                 paramsStr = paramsStr.substring(1);
             }
             paramout.write(paramsStr);
             paramout.flush();
             BufferedReader reader = new BufferedReader(new InputStreamReader(
                     connection.getInputStream(), "UTF-8"));
             String line;
             while ((line = reader.readLine()) != null) {
                 data.append(line);
             }
     
             paramout.close();
             reader.close();
         } catch (Exception e) {
             // TODO Auto-generated catch block
             e.printStackTrace();
         }
         return JSON.parse(data.toString());
     }
     ```

     

**2.post请求参数格式问题。**

- 解决办法：需要传参格式是json格式（代码同上）
- ![image-20201024174428363](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201024174428363.png)

![image-20201024174452168](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201024174452168.png)