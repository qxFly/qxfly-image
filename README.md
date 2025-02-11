qxfly的二次元图库，包含4000+手工挑选涩涩图片。

- 可使用jsdelivr等cdn服务器在线获取仓库内图片进行加载

> https://fastly.jsdelivr.net/gh/qxFly/qxfly-image@d53ceced8424d3ceb80e165d68fe59eda4444a7e/api/100018909_p0.png

- 或者使用代码获取，灵活使用，java示例如下

```java
HttpURLConnection con;
try {
    URL reqUrl = new URL("https://api.github.com/repos/qxFly/qxfly-image/git/trees/main?recursive=1");
    con = (HttpURLConnection) reqUrl.openConnection();
    con.connect();
    /*判断连接是否成功*/
    int responseCode = con.getResponseCode();
    if (responseCode != 200) {
        return "接口请求失败，" + responseCode + "错误！";
    }
} catch (IOException e) {
    e.printStackTrace();
    return "接口请求超时！";
}
try (BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(con.getInputStream()))) {
    String line;
    if ((line = bufferedReader.readLine()) != null) {
        Map map = (Map) JSON.parse(line);
        List tree = (List) map.get("tree");
        String sha = (String) map.get("sha");// commit的sha值
        List<String> names = new ArrayList<>();
        int iflag = 0;
        int df = imageManageMapper.deleteAllGithubImage();
        for (int i = 2; i < tree.size(); i++) {
            Map map1 = (Map) tree.get(i);
            String a = (String) map1.get("path");
            if (a.equals("run.bat") || a.equals("README.md")) continue;
            String name = a.split("/")[1];
            String url = "https://fastly.jsdelivr.net/gh/qxFly/qxfly-image@"+ sha + "/api/" + name;
            boolean flag = imageManageMapper.addImage(name, url);
            if (flag) {
                iflag++;
                names.add(url);
            }
        }
    } 
} catch (Exception e) {
    e.printStackTrace();
    return "读取错误！";
}
```





