# PageHelper

~~~xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.3</version>
</dependency>
~~~

Controller

~~~java
@PostMapping("/getUserPage")
public CommonResult getUserForPage(@RequestParam("pageNo") Integer pageNo, @RequestParam("pageSize") Integer pageSize) {
    return userService.getUserForPage(pageNo, pageSize);
}
~~~

Service

~~~java
@Override
public CommonResult getAllUser() {
    return new CommonResult(200, "获取所有用户列表成功", userDao.getAllUser());
}

@Override
public CommonResult getUserForPage(Integer pageNo, Integer pageSize) {
    PageHelper.startPage(pageNo, pageSize);
    List<User> allUser = userDao.getAllUser();
    PageInfo<User> userPageInfo = new PageInfo<>(allUser);
    return new CommonResult(200, "用户列表分页", userPageInfo);
}
~~~

