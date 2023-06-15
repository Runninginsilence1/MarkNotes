我另外起了一个文档。

# 物体检测的核心代码

核心流程
获取前端的文件，然后存储在服务器本地；
然后读取文件到Mat；
doPredict(src)来执行推理
推理出来后处理对应的结果；
并实现相关的逻辑， 其实就是做一件事情的所谓的三部曲。

```java
@RequestMapping("fileUpload")
    public String upload(@RequestParam("fileName") MultipartFile file, Map<String, Object> map){
        log.info("文件 [{}], 大小 [{}]", file.getOriginalFilename(), file.getSize());

        // 文件名称
        String originalFileName = file.getOriginalFilename();

        if (!upload(file, uploadPath, originalFileName)){
            map.put("msg", "上传失败！");
            return "forward:/index";
        }

        // 读取文件到Mat
        Mat src = imread(uploadPath + "/" + originalFileName);

        // 执行推理
        MatVector outs = doPredict(src);

        // 处理原始的推理结果，
        // 对检测到的每个目标，找出置信度最高的类别作为改目标的类别，
        // 还要找出每个目标的位置，这些信息都保存在ObjectDetectionResult对象中
        List<ObjectDetectionResult> results = postprocess(src, outs);

        // 释放资源
        outs.releaseReference();

        // 检测到的目标总数
        int detectNum = results.size();

        log.info("一共检测到{}个目标", detectNum);

        // 没检测到
        if (detectNum<1) {
            // 显示图片
            map.put("msg", "未检测到目标");
            // 文件名
            map.put("fileName", originalFileName);

            return "forward:/index";
        } else {
            // 检测结果页面的提示信息
            map.put("msg", "检测到" + results.size() + "个目标");
        }

        // 计算出总耗时，并输出在图片的左上角
        printTimeUsed(src);

        // 将每一个被识别的对象在图片框出来，并在框的左上角标注该对象的类别
        markEveryDetectObject(src, results);

        // 将添加了标注的图片保持在磁盘上，并将图片信息写入map（给跳转页面使用）
        saveMarkedImage(map, src);

        return "forward:/index";
    }
```
