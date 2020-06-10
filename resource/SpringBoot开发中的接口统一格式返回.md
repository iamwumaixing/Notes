#### SpringBoot开发中的接口统一格式返回

> 我在项目中做的 rest 接口全局统一格式返回的处理

- 使用了 Lombok 插件，所以需要引入依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

- 定义状态码的枚举类

```java
@Getter
public enum StatusCode {

    SUCCESS(100,"Request is successful"), // 成功返回
    FAIL(109, "Request is failed"), // 失败返回
    INNER_ERROR(500, "Unknown error"); // 未知错误

    private Integer code;

    private String message;

    StatusCode(Integer code, String message) {
        this.code = code;
        this.message = message;
    }

}
```

- 定义 Result 对象，对状态码、信息和数据进行了封装

```java
@Data
@AllArgsConstructor
public class Result {

    private Integer code;

    private String message;

    private Object data;

    public Result() {
    }

    private void setResultCode(StatusCode statusCode) {
        this.code = statusCode.getCode();
        this.message = statusCode.getMessage();
    }

    public static Result success() {
        Result result = new Result();
        result.setResultCode(StatusCode.SUCCESS);

        return result;
    }

    public static Result success(Object data) {
        Result result = new Result();
        result.setResultCode(StatusCode.SUCCESS);
        result.setData(data);

        return result;
    }

    public static Result fail() {
        Result result = new Result();
        result.setResultCode(StatusCode.FAIL);

        return result;
    }

    public static Result fail(StatusCode statusCode) {
        Result result = new Result();
        result.setResultCode(statusCode);

        return result;
    }

    public static Result fail(String message) {
        Result result = new Result();
        result.setCode(StatusCode.FAIL.getCode());
        result.setMessage(message);

        return result;
    }

}
```

- 在接口中使用

```
@PostMapping("/test")
public Result test() {
	return Result.success();
}
```

