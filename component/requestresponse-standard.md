# 请求、响应封装

Fend支持两种运行方式（FPM\Swoole），由于底层两者有一定差异，需要隔离开底层差异 

如果希望项目代码在两个模式下都能正常运行，请求和返回操作建议都使用这两个类来完成