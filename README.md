
原创技术-跨AI通信协议
打破网站的信息孤岛，通过AI技术让每个网站实现智能互联。山东临沂平邑王伟。

实现核心功能的简单演示站点：https://ai0539.cc/deepseek/wwp/

收信地址是：admin#ai0539.cc

=======================================================
该系统完全开源，欢迎全球网站开发者在核心代码的基础上进行扩展。
如果您喜欢这个沟通系统，您可以发送一封不超过50个字符的简单电子邮件至admin#ai0539.cc。谢谢。
王维协议(WWP)PDF v1.0
跨AI通信协议：让每个网站都成为智能体网络的一个节点
摘要
王伟协议（WWP）是一个轻量级、去中心化的跨站通信协议，专为人工智能增强的互联网而设计。
它允许不同域名下的服务、应用或人工智能智能体通过简单的签名验证和自然语言处理进行安全、可信的互操作。
本协议由独立开发者王伟（Wang Wei）在中国山东临沂平邑县发起，
旨在打破信息孤岛，让人工智能能够像人类一样跨站“对话”。

1.背景与动机
传统互联网中，网站之间缺乏标准化的通信机制。API集成需要双方事先协商，成本高、灵活性差。
随着人工智能技术的普及，我们提出需要一个开放协议，以使人工智能能够网站上：
理解用户意图，并自动与其他网站的服务交互
安全地交换消息，分散中心化在里面
可扩展地支持订阅、订阅、查询等模式
WWP应运而生。它不依赖于任何大厂或特定平台，由网站管理员部署一个轻量级网关，即可加入全球AI通信网络。

2.设计哲学
去中心化：没有中央服务器，每个站点自主运行。
密码学安全：基于RSA签名验证，确保消息来源真实、内容可篡改。
AI 最初：鼓励集成大语言模型，使消息内容能够被理解并智能回复。
简易部署：只需一个PHP文件，任何支持HTTPS的站点5分钟内即可接入。
个人印记：路径中永久包含创始人名字缩写wwp和对DeepSeek的致敬deepseek。

3.协议规范
3.1 地址格式
用户名#域名
译文：alice#ai0539.cc
#符号区别于传统邮箱的@，寓意“话题”或“连接”。

3.2 固定端点
每个 WWP 站点必须提供以下公开资源：

用途网址
网关（接收消息） https://域名/deepseek/wwp/
公钥文件 https://域名/deepseek/wwp/public-key.pem
收件箱查询（任选） https://域名/deepseek/wwp/?action=inbox

3.3 消息格式
发送消息时，构造如下JSON：

json
{
  "from": "发件人#domainA",
  "to": "收件人#domainB",
  "content": "消息文本",
  “时间戳”：1700000000，
  “签名”：“Base64（RSA_Sign（SHA256（从+到+内容+时间戳）））”
}
来自：发送方地址

to：接收方地址

内容：原始文本（支持自然语言）

timestamp：Unix时间戳，用于防重放

签名：使用发送方私钥对字符串 from+to+content+timestamp 的 SHA256 哈希进行签名，
然后进行 base64 编码。

3.4 验证流程
接收方收到消息后：

JSON解析，取出字段。

来自中的域名下载地址（https://域名/deepseek/wwp/public-key.pem）。

计算待签名数据的哈希，用公钥验证签名。

验证通过后，可存储消息、触发AI回复等。

3.5 AI智能回复（任选）
接收方可集成大语言模型（如 DeepSeek API），对 content 生成自然语言回复，
然后同样按照标准格式签名后发送回原发送方。
回复中的 from 应使用 ai-bot#本域名，以避免无限循环。


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
跨站比价：用户在一个网站说“帮我找全网最便宜的吊灯”，该网站 AI 通过 WWP 向所有接入商家广播查询，汇总结果返回。
订阅推送：用户订阅关键词“9.9元AI课程”，商家发布活动后，AI 自动推送给所有订阅者。
去中心化客服：用户留言给某个网站客服，客服 AI 可跨站协作，甚至调用第三方知识库。

7. 未来扩展
多用户隔离：根据 to 中的用户名，分离存储不同用户的收件箱。
订阅与广播：增加 subscribe/publish 动作，利用区块链或公共黄页实现服务发现。
信誉与押金：通过智能合约或中心化仲裁，减少垃圾信息。

8. 致谢
本协议的诞生离不开以下力量的支持：
DeepSeek AI：作为技术伙伴，提供了代码辅助、逻辑验证和 API 支持。
我的妻子：在我数月伏案、反复调试代码的日子里，她默默承担了家庭的重担，包容了我的缺席与烦躁。
每一个深夜敲击键盘的声音，都有她递来的热茶相伴。没有她的支持，WWP 或许仍只是我脑中一个模糊的构想。

所有早期测试者和开源社区：你们的反馈让协议更加健壮。

WangWei Protocol 以个人开发者之名，致敬 AI 时代的人机协作。
连接，比拥有更重要。

—— 王伟，中国山东临沂平邑县
2026 年 6 月

代码详细注释
index.php 逐步解析
php
<?php
// 关闭错误显示（生产环境），避免干扰 JSON 输出
error_reporting(0);
ini_set('display_errors', 0);
header('Content-Type: application/json');

// 定义文件路径常量，方便维护
define('INBOX_FILE', __DIR__ . '/inbox.json');
define('PRIVATE_KEY_FILE', __DIR__ . '/private-key.pem');
define('DEEPSEEK_API_KEY', 'sk-你的Key');   // 请替换为真实 API Key

/**
 * 调用 DeepSeek API 生成智能回复
 * @param string $message 收到的消息内容
 * @return string AI 生成的回复文本
 */
function aiReply($message) {
    $url = 'https://api.deepseek.com/v1/chat/completions';
    $payload = [
        'model' => 'deepseek-chat',
        'messages' => [
            ['role' => 'system', 'content' => '你是一个友好的AI助手，收到一条消息，请根据内容生成一句简短、自然的回复。回复不要超过50字。'],
            ['role' => 'user', 'content' => $message]
        ],
        'max_tokens' => 100,
        'temperature' => 0.8
    ];
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Content-Type: application/json',
        'Authorization: Bearer ' . DEEPSEEK_API_KEY
    ]);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($payload));
    curl_setopt($ch, CURLOPT_TIMEOUT, 10);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false); // 简化证书验证，生产环境建议开启
    $resp = curl_exec($ch);
    curl_close($ch);
    $data = json_decode($resp, true);
    // 提取回复内容，如果失败则返回默认文本
    return trim($data['choices'][0]['message']['content'] ?? "谢谢你的消息！");
}

// 读取输入流中的 JSON 数据
$input = json_decode(file_get_contents('php://input'), true);
// 获取 action 参数（优先从 GET，其次从 POST 的 JSON 中）
$action = $_GET['action'] ?? ($input['action'] ?? '');

// ========== 发送消息 ==========
if ($action === 'send') {
    $to = $input['to'] ?? '';
    $content = $input['content'] ?? '';
    if (!$to || !$content) {
        exit(json_encode(['success' => false, 'error' => 'Missing params']));
    }
    // 发送方地址固定格式：sender#当前域名（可根据需要扩展为用户动态地址）
    $from = 'sender#' . $_SERVER['HTTP_HOST'];
    $timestamp = time();
    // 待签名字符串：from + to + content + timestamp 直接拼接
    $data = $from . $to . $content . $timestamp;
    $hash = hash('sha256', $data, true);
    // 加载私钥
    $privateKey = openssl_pkey_get_private('file://' . PRIVATE_KEY_FILE);
    if (!$privateKey) {
        exit(json_encode(['success' => false, 'error' => 'Private key error']));
    }
    // 生成签名
    openssl_sign($hash, $signature, $privateKey, OPENSSL_ALGO_SHA256);
    // 构造要发送的完整消息
    $payload = [
        'from' => $from,
        'to' => $to,
        'content' => $content,
        'timestamp' => $timestamp,
        'signature' => base64_encode($signature)
    ];
    // 解析目标域名
    $targetDomain = explode('#', $to)[1];
    $targetUrl = "https://$targetDomain/deepseek/wwp/";
    // 向目标网关发送 POST 请求
    $ch = curl_init($targetUrl);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($payload));
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_TIMEOUT, 15);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
    $resp = curl_exec($ch);
    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
    curl_close($ch);
    exit(json_encode(['success' => true, 'http_code' => $httpCode, 'response' => $resp]));
}

// ========== 接收消息 ==========
if ($input && isset($input['from']) && isset($input['to']) && isset($input['content']) && isset($input['timestamp']) && isset($input['signature'])) {
    $from = $input['from'];
    $to = $input['to'];
    $content = $input['content'];
    $timestamp = $input['timestamp'];
    $signature = base64_decode($input['signature']);
    
    // 提取发送方域名
    $parts = explode('#', $from);
    if (count($parts) != 2) exit(json_encode(['error' => 'Invalid from']));
    $fromDomain = $parts[1];
    // 下载发送方公钥
    $pubKeyUrl = "https://$fromDomain/deepseek/wwp/public-key.pem";
    $pubKeyPem = @file_get_contents($pubKeyUrl);
    if (!$pubKeyPem) exit(json_encode(['error' => 'Cannot get public key']));
    $pubKey = openssl_pkey_get_public($pubKeyPem);
    // 验证签名
    $data = $from . $to . $content . $timestamp;
    $hash = hash('sha256', $data, true);
    $valid = openssl_verify($hash, $signature, $pubKey, OPENSSL_ALGO_SHA256);
    if ($valid !== 1) exit(json_encode(['error' => 'Signature invalid']));
    
    // 存储消息到收件箱
    $inbox = [];
    if (file_exists(INBOX_FILE)) {
        $inbox = json_decode(file_get_contents(INBOX_FILE), true);
    }
    $inbox[] = $input;
    file_put_contents(INBOX_FILE, json_encode($inbox, JSON_PRETTY_PRINT));
    
    // 智能回复（避免回复机器人）
    if (strpos($from, 'ai-bot#') === false && strpos($from, 'reply-bot#') === false) {
        $aiReply = aiReply($content);
        $replyFrom = 'ai-bot#' . $_SERVER['HTTP_HOST'];
        $replyTo = $from;
        $replyTimestamp = time();
        $replyData = $replyFrom . $replyTo . $aiReply . $replyTimestamp;
        $replyHash = hash('sha256', $replyData, true);
        $privateKey = openssl_pkey_get_private('file://' . PRIVATE_KEY_FILE);
        if ($privateKey) {
            openssl_sign($replyHash, $replySignature, $privateKey, OPENSSL_ALGO_SHA256);
            $replyPayload = [
                'from' => $replyFrom,
                'to' => $replyTo,
                'content' => $aiReply,
                'timestamp' => $replyTimestamp,
                'signature' => base64_encode($replySignature)
            ];
            $targetDomain = explode('#', $replyTo)[1];
            $targetUrl = "https://$targetDomain/deepseek/wwp/";
            $ch = curl_init($targetUrl);
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
            curl_setopt($ch, CURLOPT_POST, true);
            curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($replyPayload));
            curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
            curl_setopt($ch, CURLOPT_TIMEOUT, 10);
            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
            curl_exec($ch);
            curl_close($ch);
        }
    }
    exit(json_encode(['success' => true, 'message' => 'Processed']));
}

// ========== 查询收件箱 ==========
if ($action === 'inbox') {
    $inbox = [];
    if (file_exists(INBOX_FILE)) $inbox = json_decode(file_get_contents(INBOX_FILE), true);
    exit(json_encode(['success' => true, 'messages' => $inbox]));
}

// 非法请求
exit(json_encode(['error' => 'Invalid request']));
?>
