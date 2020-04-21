### 前言

##### 在Java工作开发中会遇到各种各样的坑，所以总结起来会很有帮助，别人也可以再业余时间学习，本篇文章会以问题的方式解答，力求详细，知其然知其所以为然。

#### 1.在使用JPA操纵数据库时，遇到数据库字段tinyint(1)转化成实体时是布尔值，此时往往不符合业务场景
   
   1. 在mysql中对 TINYINT(1) 和 BOOLEAN 的处理是一样的
   2. 修改数据库表结构为tinyint(2)，这个方法简单
   3. 在jdbc的url后面添加了?tinyInt1isBit=false,问题解决

#### 2.在使用redis做缓存服务时，存储数据尽量用JSON格式，方便语言的迁移，例如从PHP的代码重构成JAVA

#### 3.JAVA除法运算如何保留小数？
   > 1.JAVA中用/除法是取整数操作，不能保留小数，例如
   
    int a=4;
    int b=3;
    float c = (float) a/b;
    System.out.print(c); //输出：1
   
   > 2.可以使用如下方式，例如
   
     int a=4;
     int b=3;
     float c = (float) a / (float) b;
     System.out.print(c);//输出：1.3333334

#### 4.new Date()插入mysql数据库时多了一秒
   1. 在使用new Date() 插入数据库时，查询出来比实际多了一秒，mysql 表字段设置为datetime类型，当时间精确到秒时，如果毫秒大于500时，会自动进位。
   2. 解决方法为格式化后再插入数据库。
    或者使用Jpa提供的SaveAndFlush()，刷新实体进行后台的操作。
    
    /**
     * 解决Date存入数据库中毫秒>500时进一位。
     */
    public static Date nowDb() {
        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.MILLISECOND, 0);
        return calendar.getTime();
    }

#### 4.UTF-8和GBK格式字符串相互转化方法
   > 1.博客地址：[UTF-8与GBK互转，为什么会乱码](https://blog.csdn.net/luomao2012/article/details/105671089)
   
   > 2.代码示例如下
   
        /**
         * 1.UTF-8编码转换成GBK
         *
         * @param utf8
         * @return
         */
        public static String utf8ToGbk(String utf8) {
            try {
                return new String(utf8.getBytes(CHARSET_GBK), CHARSET_GBK).trim();
            } catch (UnsupportedEncodingException e) {
                logger.error("[EncodeUtils][utf8ToGbk] Code Convert utf8 To gbk String Failed, utf8={}", utf8);
                throw new ResponseException(ResponseError.E_PARAM, ResponseErrorMessage.E_PARAM.getMessage());
            }
        }
    
        /**
         * 2.GBK编码转换成UTF-8
         *
         * @param gbk
         * @return
         */
        public static String gbkToUtf8(String gbk) {
            return new String(gbk.getBytes(StandardCharsets.UTF_8), StandardCharsets.UTF_8).trim();
        }