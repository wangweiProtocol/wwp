第一步，创建文件夹deepseek和wwp,最终路径为：域名/deepseek/wwp
文件全部放在wwp目录即可。

第二步：需要修改的核心配置（/deepseek/wwp/config.php）

KEY，后台管理员账号，密码
<?php
// config.php
define('SITE_DOMAIN', '新域名.com');                 // 修改为新的域名
define('DEEPSEEK_API_KEY', 'sk-你的新Key');        // 替换为你的 DeepSeek API Key（多个网站可以使用同一个）
define('DB_FILE', __DIR__ . '/data.db');           // 数据库文件路径（保持相对路径即可）
define('PUBLIC_KEY_PATH', __DIR__ . '/public-key.pem');
define('PRIVATE_KEY_PATH', __DIR__ . '/private-key.pem');
define('PEER_SITES', [                             // 如果需要与其他 WWP 站点互通，添加对方域名
    'ai0539.cc' => 'https://ai0539.cc/deepseek/wwp/',
    // 其他站点...
]);
define('SHARED_SECRET', '你自己的随机字符串');      // 签名密钥，可随机生成
define('ADMIN_USER', '管理员用户名');                 // 管理员用户名（可改）
define('ADMIN_PASS', '管理员密码');                 // 管理员密码（可改）
?>

第三步，在宝塔WWP目录右键，使用终端生产公钥和私钥：

bash
cd /www/wwwroot/新域名.com/deepseek/wwp
openssl genrsa -out private-key.pem 2048
chmod 600 private-key.pem
openssl rsa -in private-key.pem -pubout -out public-key.pem
chmod 644 public-key.pem
chown www:www private-key.pem public-key.pem

第四步，目录设置权限：

cd /www/wwwroot/新域名.com
chown -R www:www deepseek
chmod 755 deepseek
chmod 755 deepseek/wwp
chmod 644 deepseek/wwp/*.php
chmod 600 deepseek/wwp/private-key.pem

访问 https://新域名.com/deepseek/wwp/index.html，应该出现登录/注册页面。
以上操作有问题，自己问AI即可，推荐使用deepseek



原创技术——跨AI通讯协议
打破网站信息孤岛，让每一个网站通过AI技术，实现智能互联。
山东，临沂，平邑，王伟。


本系统完全开源，欢迎全球网站开发者在核心代码基础上做出扩展，
如果你喜欢这个通讯系统，可以向admin#ai0539.cc发送一个简单的50字符以内的邮件。谢谢。

This system is fully open-source, and we welcome global website developers to extend it based on the core code. If you like this communication system, you can send a simple email with no more than 50 characters to admin#ai0539.cc. Thank you.
==================================================

WangWei Protocol (WWP) 白皮书 v1.0
跨 AI 通信协议：让每个网站都成为智能体网络的一个节点
摘要
WangWei Protocol (WWP) 是一个轻量级、去中心化的跨站通信协议，专为 AI 增强的互联网而设计。

它允许不同域名下的服务、应用或 AI 智能体通过简单的签名验证和自然语言处理进行安全、可信的互操作。

本协议由独立开发者王伟（Wang Wei）在中国山东临沂平邑县发起，

旨在打破信息孤岛，让 AI 能够像人类一样跨站“对话”。

1. 背景与动机
传统互联网中，网站之间缺乏标准化的通信机制。

API 集成需要双方预先协商，成本高、灵活性差。

随着 AI 技术的普及，我们迫切需要一个开放协议，使得任意网站上的 AI 能够：

理解用户意图，并自动与其它网站的服务交互

安全地交换消息，无需中心化中介

可扩展地支持订阅、推送、查询等模式

WWP 应运而生。它不依赖任何大厂或特定平台，

仅需网站管理员部署一个轻量级网关，即可加入全球 AI 通信网络。

2. 设计哲学
去中心化：没有中央服务器，每个站点自主运行。

密码学安全：基于 RSA 签名验证，确保消息来源真实、内容未被篡改。

AI 原生：鼓励集成大语言模型，使消息内容能够被理解并智能回复。

易部署：只需一个 PHP 文件，任何支持 HTTPS 的站点 5 分钟内即可接入。

个人印记：路径中永久包含创始人名字缩写 wwp 和对 DeepSeek 的致敬 deepseek。

3. 协议规范
3.1 地址格式
用户名#域名
示例：alice#ai0539.cc
# 符号区别于传统邮箱的 @，寓意“话题”或“连接”。

3.2 固定端点
每个 WWP 站点必须提供以下公开资源：

用途	URL
网关（接收消息）	https://域名/deepseek/wwp/
公钥文件	https://域名/deepseek/wwp/public-key.pem
收件箱查询（可选）	https://域名/deepseek/wwp/?action=inbox

3.3 消息格式
发送消息时，构造如下 JSON：

json
{
  "from": "sender#domainA",
  "to": "recipient#domainB",
  "content": "消息文本",
  "timestamp": 1700000000,
  "signature": "Base64(RSA_Sign(SHA256(from+to+content+timestamp)))"
}
from：发送方地址

to：接收方地址

content：原始文本（支持自然语言）

timestamp：Unix 时间戳，用于防重放

signature：使用发送方私钥对字符串 from+to+content+timestamp 的 SHA256 哈希进行签名，然后 base64 编码。

3.4 验证流程
接收方收到消息后：

解析 JSON，提取字段。

从 from 中的域名下载公钥（https://域名/deepseek/wwp/public-key.pem）。

计算待签名数据的哈希，用公钥验证 signature。

验证通过后，可存储消息、触发 AI 回复等。

3.5 AI 智能回复（可选）
接收方可集成大语言模型（如 DeepSeek API），对 content 生成自然语言回复，
然后同样按照标准格式签名后发送回原发送方。回复中的 from 应使用 ai-bot#本域名，以避免无限循环。

4. 参考实现（PHP）
4.1 目录结构
text
/deepseek/wwp/
├── index.php       # 网关
├── private-key.pem # 私钥（600权限，站长生成）
├── public-key.pem  # 公钥（644权限）
└── inbox.json      # 收件箱（自动创建）
4.2 生成密钥对
bash
cd /path/to/deepseek/wwp
openssl genrsa -out private-key.pem 2048
chmod 600 private-key.pem
openssl rsa -in private-key.pem -pubout -out public-key.pem
chmod 644 public-key.pem
chown -R www:www .
4.3 核心代码解释
常量定义：指定收件箱文件、私钥路径和 DeepSeek API Key。

php
define('INBOX_FILE', __DIR__ . '/inbox.json');
define('PRIVATE_KEY_FILE', __DIR__ . '/private-key.pem');
define('DEEPSEEK_API_KEY', 'sk-...');
AI 回复函数：

php
function aiReply($message) {
    // 调用 DeepSeek 聊天 API，要求生成简短自然回复
    // 返回生成的文本
}
发送消息分支 (action=send)：

接收目标地址和内容

构造 from（格式 sender#当前域名）

计算签名

向目标域名的网关发起 POST 请求

返回对方响应

接收消息分支：

检查是否存在 from, to, content, timestamp, signature 字段

解析发送方域名，下载公钥

验证签名

存储到 inbox.json

调用 aiReply 生成回复，并同样签名后发送回发送方

查询收件箱 (action=inbox)：

读取 inbox.json 并返回 JSON 格式的消息列表

错误处理：所有未匹配的请求返回 {"error":"Invalid request"}。

5. 安全性分析
防伪造：只有持有私钥的站点才能生成有效签名。

防篡改：消息内容一旦被修改，签名验证失败。

防重放：建议接收方记录已处理的时间戳，拒绝超出允许时间窗口（如 5 分钟）或重复的 timestamp。

传输安全：强制 HTTPS 加密通信。

6. 使用场景示例
跨站比价：用户在一个网站说“帮我找全网最便宜的吊灯”，
该网站 AI 通过 WWP 向所有接入商家广播查询，汇总结果返回。

订阅推送：用户订阅关键词“9.9元AI课程”，商家发布活动后，AI 自动推送给所有订阅者。

去中心化客服：用户留言给某个网站客服，客服 AI 可跨站协作，甚至调用第三方知识库。

7. 未来扩展
多用户隔离：根据 to 中的用户名，分离存储不同用户的收件箱。

订阅与广播：增加 subscribe/publish 动作，利用区块链或公共黄页实现服务发现。

信誉与押金：通过智能合约或中心化仲裁，减少垃圾信息。

8. 致谢
本协议的诞生离不开以下力量的支持：

DeepSeek AI：作为技术伙伴，提供了代码辅助、逻辑验证和 API 支持。

我的妻子：在我数月伏案、反复调试代码的日子里，她默默承担了家庭的重担，
包容了我的缺席与烦躁。每一个深夜敲击键盘的声音，都有她递来的热茶相伴。
没有她的支持，WWP 或许仍只是我脑中一个模糊的构想。

所有早期测试者和开源社区：你们的反馈让协议更加健壮。

WangWei Protocol 以个人开发者之名，致敬 AI 时代的人机协作。
连接，比拥有更重要。

—— 王伟，中国山东临沂平邑县
2026 年 6 月


