# Maven生成文件中包含的jar在依赖中查询不到的问题

项目在测试环境打包部署，启动的时候报日志的多个实现类问题，即slf4j和logback冲突。
但是在本地启动是okay的，在maven dependency:tree查看的信息中，并没有引入logback。
查看本地打包，确实有logbck。
通过maven dependency:tree -Dverbose打印详细日志，发现logback在另外一个同学引入的spring-boo-starter-aop中引入。
排除后，问题修复。