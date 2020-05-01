# [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#配置-fastdfs-java-客户端)配置 FastDFS Java 客户端

## [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-分布式文件系统-FastDFS Java 客户端](https://www.bilibili.com/video/av35251691/)

## [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#创建项目)创建项目

创建一个名为 `myshop-service-upload` 的服务提供者项目

## [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#安装-fastdfs-java-客户端)安装 FastDFS Java 客户端

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#从-github-克隆源码)从 GitHub 克隆源码

```text
git clone https://github.com/happyfish100/fastdfs-client-java.git
```

1

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#在项目中添加依赖)在项目中添加依赖

```text
<!-- FastDFS Begin -->
<dependency>
    <groupId>org.csource</groupId>
    <artifactId>fastdfs-client-java</artifactId>
    <version>1.27-SNAPSHOT</version>
</dependency>
<!-- FastDFS End -->
```

1
2
3
4
5
6
7

## [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#创建-fastdfs-工具类)创建 FastDFS 工具类

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#定义文件存储服务接口)定义文件存储服务接口

```text
package com.funtl.myshop.service.upload.fastdfs;

/**
 * 文件存储服务接口
 * <p>Title: StorageService</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/8/14 5:22
 */
public interface StorageService {
    /**
     * 上传文件
     *
     * @param data    文件的二进制内容
     * @param extName 扩展名
     * @return 上传成功后返回生成的文件id；失败，返回null
     */
    public String upload(byte[] data, String extName);

    /**
     * 删除文件
     *
     * @param fileId 被删除的文件id
     * @return 删除成功后返回0，失败后返回错误代码
     */
    public int delete(String fileId);
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#实现文件存储服务接口)实现文件存储服务接口

```text
package com.funtl.myshop.service.upload.fastdfs;

import org.csource.common.NameValuePair;
import org.csource.fastdfs.ClientGlobal;
import org.csource.fastdfs.StorageClient1;
import org.csource.fastdfs.StorageServer;
import org.csource.fastdfs.TrackerClient;
import org.csource.fastdfs.TrackerGroup;
import org.csource.fastdfs.TrackerServer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Value;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * 文件存储服务实现
 * <p>Title: FastDFSStorageService</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/8/14 5:27
 */
public class FastDFSStorageService implements StorageService, InitializingBean {
    private static final Logger logger = LoggerFactory.getLogger(FastDFSStorageService.class);

    private TrackerClient trackerClient;

    @Value("${storage.fastdfs.tracker_server}")
    private String trackerServer;

    @Override
    public String upload(byte[] data, String extName) {
        TrackerServer trackerServer = null;
        StorageServer storageServer = null;
        StorageClient1 storageClient1 = null;
        try {
            NameValuePair[] meta_list = null; // new NameValuePair[0];

            trackerServer = trackerClient.getConnection();
            if (trackerServer == null) {
                logger.error("getConnection return null");
            }
            storageServer = trackerClient.getStoreStorage(trackerServer);
            storageClient1 = new StorageClient1(trackerServer, storageServer);
            String fileid = storageClient1.upload_file1(data, extName, meta_list);
            logger.debug("uploaded file <{}>", fileid);
            return fileid;
        } catch (Exception ex) {
            logger.error("Upload fail", ex);
            return null;
        } finally {
            if (storageServer != null) {
                try {
                    storageServer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (trackerServer != null) {
                try {
                    trackerServer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            storageClient1 = null;
        }
    }

    @Override
    public int delete(String fileId) {
//        System.out.println("deleting ....");
        TrackerServer trackerServer = null;
        StorageServer storageServer = null;
        StorageClient1 storageClient1 = null;
        int index = fileId.indexOf('/');
        String groupName = fileId.substring(0, index);
        try {
            trackerServer = trackerClient.getConnection();
            if (trackerServer == null) {
                logger.error("getConnection return null");
            }
            storageServer = trackerClient.getStoreStorage(trackerServer, groupName);
            storageClient1 = new StorageClient1(trackerServer, storageServer);
            int result = storageClient1.delete_file1(fileId);
            return result;
        } catch (Exception ex) {
            logger.error("Delete fail", ex);
            return 1;
        } finally {
            if (storageServer != null) {
                try {
                    storageServer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (trackerServer != null) {
                try {
                    trackerServer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            storageClient1 = null;
        }
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        File confFile = File.createTempFile("fastdfs", ".conf");
        PrintWriter confWriter = new PrintWriter(new FileWriter(confFile));
        confWriter.println("tracker_server=" + trackerServer);
        confWriter.close();
        ClientGlobal.init(confFile.getAbsolutePath());
        confFile.delete();
        TrackerGroup trackerGroup = ClientGlobal.g_tracker_group;
        trackerClient = new TrackerClient(trackerGroup);

        logger.info("Init FastDFS with tracker_server : {}", trackerServer);
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
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#文件存储服务工厂类)文件存储服务工厂类

```text
package com.funtl.myshop.service.upload.fastdfs;

import org.springframework.beans.factory.FactoryBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.beans.factory.config.AutowireCapableBeanFactory;

import java.util.HashMap;
import java.util.Map;

/**
 * 文件存储服务工厂类
 * <p>Title: StorageFactory</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/8/14 5:28
 */
public class StorageFactory implements FactoryBean<StorageService> {

    @Autowired
    private AutowireCapableBeanFactory acbf;

    /**
     * 存储服务的类型，目前仅支持fastdfs
     */
    @Value("${storage.type}")
    private String type;

    private Map<String, Class<? extends StorageService>> classMap;

    public StorageFactory() {
        classMap = new HashMap<>();
        classMap.put("fastdfs", FastDFSStorageService.class);
    }

    @Override
    public StorageService getObject() throws Exception {
        Class<? extends StorageService> clazz = classMap.get(type);
        if (clazz == null) {
            throw new RuntimeException("Unsupported storage type [" + type + "], valid are " + classMap.keySet());
        }

        StorageService bean = clazz.newInstance();
        acbf.autowireBean(bean);
        acbf.initializeBean(bean, bean.getClass().getSimpleName());
        return bean;
    }

    @Override
    public Class<?> getObjectType() {
        return StorageService.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
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
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#配置文件存储服务工厂类)配置文件存储服务工厂类

```text
package com.funtl.myshop.service.upload.fastdfs;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * Java 配置方式定义 StorageFactory 的 Bean 使其可以被依赖注入
 * <p>Title: FastDFSConfiguration</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/8/14 5:28
 */
@Configuration
public class FastDFSConfiguration {
    @Bean
    public StorageFactory storageFactory() {
        return new StorageFactory();
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

## [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#创建-fastdfs-控制器)创建 FastDFS 控制器

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#增加云配置)增加云配置

```text
fastdfs.base.url: http://192.168.75.128:8888/
storage:
  type: fastdfs
  fastdfs:
    tracker_server: 192.168.75.128:22122
```

1
2
3
4
5

### [#](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html#控制器代码)控制器代码

```text
package com.funtl.myshop.service.upload.controller;

import com.funtl.itoken.service.upload.fastdfs.StorageService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@CrossOrigin(origins = "*", maxAge = 3600)
@RestController
public class UploadController {
    @Value("${fastdfs.base.url}")
    private String FASTDFS_BASE_URL;

    @Autowired
    private StorageService storageService;

    /**
     * 文件上传
     *
     * @param dropFile    Dropzone
     * @param editorFiles wangEditor
     * @return
     */
    @RequestMapping(value = "upload", method = RequestMethod.POST)
    public Map<String, Object> upload(MultipartFile dropFile, MultipartFile[] editorFiles) {
        Map<String, Object> result = new HashMap<>();

        // Dropzone 上传
        if (dropFile != null) {
            result.put("fileName", writeFile(dropFile));
        }

        // wangEditor 上传
        if (editorFiles != null && editorFiles.length > 0) {
            List<String> fileNames = new ArrayList<>();

            for (MultipartFile editorFile : editorFiles) {
                fileNames.add(writeFile(editorFile));
            }

            result.put("errno", 0);
            result.put("data", fileNames);
        }

        return result;
    }

    /**
     * 将图片写入指定目录
     *
     * @param multipartFile
     * @return 返回文件完整路径
     */
    private String writeFile(MultipartFile multipartFile) {
        // 获取文件后缀
        String oName = multipartFile.getOriginalFilename();
        String extName = oName.substring(oName.lastIndexOf(".") + 1);

        // 文件存放路径
        String url = null;
        try {
            String uploadUrl = storageService.upload(multipartFile.getBytes(), extName);
            url = FASTDFS_BASE_URL + uploadUrl;
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 返回文件完整路径
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
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81

上次更新: 2018-12-31 18:51:48

← [基于 Docker 安装 FastDFS](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html)[跨域现象 ](https://funtl.com/zh/apache-dubbo-codeing/跨域现象.html)→