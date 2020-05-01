# HttpServletUtils

```text
package com.funtl.itoken.common.utils;

import javax.servlet.http.HttpServletRequest;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

public class HttpServletUtils {

    /**
     * 获取完整请求路径，带请求参数
     *
     * @param request
     * @return
     */
    public static String getFullPath(HttpServletRequest request) {
        StringBuffer uri = request.getRequestURL();
        String url = uri.toString();
        // 获取所有请求,返回 Map 集合
        Map<String, String[]> map = request.getParameterMap();
        Set<Map.Entry<String, String[]>> entry = map.entrySet();
        Iterator<Map.Entry<String, String[]>> iterator = entry.iterator();

        // 遍历集合
        StringBuffer sb = new StringBuffer();
        while (iterator.hasNext()) {
            Map.Entry<String, String[]> item = iterator.next();
            //请求名
            String key = item.getKey();
            //请求值
            for (String value : item.getValue()) {
                // 拼接每个请求参数 key=value&
                sb.append(key + "=" + value + "&");
            }
        }

        String string = sb.toString();
        // 拼接 URL, URL?key=value&key=value& 并且去掉最后一个 &
        url = url + "?" + string.substring(0, string.lastIndexOf("&"));
        return url;
    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42

上次更新: 2018-12-31 18:51:48

[nth-tabs ](https://funtl.com/zh/supplement2/nth-tabs.html)→