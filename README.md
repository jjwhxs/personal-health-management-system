### 系统介绍
基于SpringBoot和Vue实现的个人健康管理系统采用前后端分离的架构方式。系统设计了两种角色，分别是用户和管理员，每种角色拥有不同的权限。用户可以在系统中进行注册、登录、健康概览、身体指数记录、血压记录、血糖记录、水分摄入记录、体温记录、饮食记录、运动记录、健康知识、个人信息等功能模块的操作。管理员可以在系统中进行登录、健康数据、健康记录、用户管理、饮食记录管理、运动记录管理、血糖记录管理、血压记录管理、水分摄入管理、体温记录管理、身体指数管理、运动类型、文章管理等功能模块的操作。

### 技术选型
开发工具：idea2022.3+webstorm2021.1

运行环境：jdk17+maven3.6.0+mysql8.0+nodejs18.16.0

服务端技术：springboot+mybatis-plus+jwt+fastjson

前端技术：html+css+vue+axios+element-ui+echarts

### 成果展示
系统登录/注册
<img width="1910" height="980" alt="系统登录注册" src="https://github.com/user-attachments/assets/606ab494-6677-4eeb-94b3-03bc475c1b09" />

用户->健康数据
<img width="1910" height="1232" alt="用户-健康数据" src="https://github.com/user-attachments/assets/d15d0f00-7f1e-4d2e-92d0-e5d8a20137dd" />

用户->血压记录
<img width="1910" height="1532" alt="用户-血压记录" src="https://github.com/user-attachments/assets/081ff430-a756-4d8b-b6b5-89ae72ab0788" />

用户->水分摄入记录
<img width="1910" height="1828" alt="用户-水分摄入记录" src="https://github.com/user-attachments/assets/5f080abf-c074-42f7-91a5-2294875c1fe7" />

用户->运动记录
<img width="1910" height="1636" alt="用户-运动记录" src="https://github.com/user-attachments/assets/af161a45-ae9a-4f84-b86b-af94a9076cbd" />

用户->健康知识
<img width="1910" height="1210" alt="用户-健康知识" src="https://github.com/user-attachments/assets/c533c725-1624-4102-9ca8-a65d95749765" />

管理员->饮食记录管理
<img width="1910" height="1418" alt="管理员-饮食记录管理" src="https://github.com/user-attachments/assets/f3dd8b76-86e7-4ab9-ab29-9fede1c84b55" />

### 源码展示
@Tag(name = "运动记录管理接口")

@RestController

@RequestMapping("/exercise")

public class ExerciseRecordController {

    @Resource
    private ExerciseRecordService exerciseRecordService;

    @Operation(summary = "分页查询运动记录")
    @GetMapping("/page")
    public Result<Page<ExerciseRecord>> getExerciseRecordPage(
            @RequestParam(required = false) Integer exerciseType,
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime startTime,
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime endTime,
            @RequestParam(defaultValue = "1") Integer currentPage,
            @RequestParam(defaultValue = "10") Integer size) {
        Long userId = JwtTokenUtils.getCurrentUser().getId();
        Page<ExerciseRecord> page = exerciseRecordService.getExerciseRecordPage(userId, exerciseType, startTime,   
                                    endTime, currentPage, size);
        return Result.success(page);
    }

    @Operation(summary = "管理员查询所有用户的运动记录")
    @GetMapping("/admin/page")
    public Result<Page<ExerciseRecord>> getAllExerciseRecordPage(
            @RequestParam(required = false) Long userId,
            @RequestParam(required = false) Integer exerciseType,
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime startTime,
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime endTime,
            @RequestParam(defaultValue = "1") Integer currentPage,
            @RequestParam(defaultValue = "10") Integer size) {
        // 此接口仅管理员可用，权限检查在服务层进行
        Page<ExerciseRecord> page = exerciseRecordService.getAllExerciseRecordPage(userId, exerciseType, startTime, 
                                    endTime, currentPage, size);
        return Result.success(page);
    }

    @Operation(summary = "添加运动记录")
    @PostMapping
    public Result<?> addExerciseRecord(@RequestBody ExerciseRecord exerciseRecord) {
        exerciseRecordService.addExerciseRecord(exerciseRecord);
        return Result.success();
    }

    @Operation(summary = "更新运动记录")
    @PutMapping("/{id}")
    public Result<?> updateExerciseRecord(@PathVariable Long id, @RequestBody ExerciseRecord exerciseRecord) {
        exerciseRecord.setId(id);
        exerciseRecordService.updateExerciseRecord(exerciseRecord);
        return Result.success();
    }

    @Operation(summary = "删除运动记录")
    @DeleteMapping("/{id}")
    public Result<?> deleteExerciseRecord(@PathVariable Long id) {
        exerciseRecordService.deleteExerciseRecord(id);
        return Result.success();
    }
    
    @Operation(summary = "获取运动统计数据")
    @GetMapping("/stats")
    public Result<Map<String, Object>> getExerciseStats(
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime startTime,
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime endTime) {
        Long userId = JwtTokenUtils.getCurrentUser().getId();
        Map<String, Object> stats = exerciseRecordService.getExerciseStats(userId, startTime, endTime);
        return Result.success(stats);
    }
    
    @Operation(summary = "管理员获取所有用户的运动记录统计数据")
    @GetMapping("/admin/stats")
    public Result<Map<String, Object>> getAllExerciseStats(
            @RequestParam(required = false) Long userId,
            @RequestParam(required = false) Integer exerciseType,
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime startTime,
            @RequestParam(required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime endTime) {
        // 此接口仅管理员可用，权限检查在服务层进行
        Map<String, Object> stats = exerciseRecordService.getAllExerciseStats(userId, exerciseType, startTime, 
                                    endTime);
        return Result.success(stats);
    }
    
    @Operation(summary = "获取最新运动记录")
    @GetMapping("/latest")
    public Result<ExerciseRecord> getLatestExerciseRecord(
            @RequestParam(required = false) Long userId) {
        // 如果未提供用户ID，则使用当前登录用户
        if (userId == null) {
            userId = JwtTokenUtils.getCurrentUser().getId();
        }
        ExerciseRecord latestRecord = exerciseRecordService.getLatestExerciseRecord(userId);
        return Result.success(latestRecord);
    }
}

### 账号地址及其它说明
1、地址说明

登录页：http://localhost:5173/

2、账号说明

用户：test/123456

管理员：admin/123456

3、目录结构展示

<img width="569" height="178" alt="目录结构展示" src="https://github.com/user-attachments/assets/187d532a-dfa4-40cd-8553-d514abf06b66" />

4、项目结构展示

<img width="1694" height="629" alt="项目结构展示" src="https://github.com/user-attachments/assets/65ff8ef5-ac6c-4b85-aa10-b20e6f53804c" />

5、运行步骤

1）创建数据库、导入sql脚本 

2）修改application.properties中的数据库配置文件，启动服务端

3）在前端根目录下打开cmd，执行npm install下载依赖

4）下载完毕后启动前端npm run dev，访问端口

### 获取方式(可远程调试)
访问链接：https://mbd.pub/o/bread/mbd-YZWbk5lqaw==

若资源获取失败，可添加happy35596339(vx)或2061772307(qq)进行交流
