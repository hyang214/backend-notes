## Lists.transform导致异常被报错无法正常捕获

+ 时间：2019.04.26
+ 场景：
  + service层使用了```Lists.transform```进行object转换
  + 转换代码中，抛出了自定义的```BusinessException```
  + 在异常触发的情况下，```@ControllerAdvice```定义的拦截```BusinessException```的方法没有触发		
  + 实际的异常
    ```
    org.apache.ibatis.builder.BuilderException: Error evaluating expression 'ew.sqlSegment != null and ew.sqlSegment != '' and ew.nonEmptyOfWhere'. Cause: org.apache.ibatis.ognl.OgnlException: sqlSegment [com.tal.seg.ts.common.base.exception.BusinessException: 4000004:错误消息]
    ```
+ 原因：
  + ```Lists.transform```返回的List，真正的转换，是在使用其中元素的时候
  + service层定义了对象转换，但是未执行
  + list被传递到了Repository层，然后进行取用数据，触发异常，抛出```BusinessException```
  + 异常被mybatis的AOP拦截，进行包装，封装到```org.apache.ibatis.builder.BuilderException```
  + ControllerAdvice中未定义对于```BuilderException```的拦截进入兜底的```Exception```拦截
+ 解决方案：

  + 不使用Guava的Lists.transform
  + 使用lambda表达式替换对于转换







