---
title: php开发-Tcp连接
tags: PHP开发
categories:
  - PHP开发
abbrlink: 680ac944
date: 2024-06-13 09:09:50
---

# 封装的连接类
```php

<?php

namespace SimpleSocket;

set_error_handler(function ($errno, $errstr, $errfile, $errline) {
  throw new \ErrorException($errstr, $errno, 0, $errfile, $errline);
});

class Connector
{
  private $_socket = null;

  public function connectTcp(string $address, int $port, int $timeout = 30)
  {
    try {
      $this->_socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
    } catch (\Exception $e) {
      throw new \Exception($e->getMessage(), 10);
    }

    if ($timeout !== 30) {
      try {
        socket_set_option($this->_socket, SOL_SOCKET, SO_SNDTIMEO, ['sec' => $timeout, 'usec' => 0]);
        socket_set_option($this->_socket, SOL_SOCKET, SO_RCVTIMEO, ['sec' => $timeout, 'usec' => 0]);
      } catch (\Exception $e) {
        throw new \Exception($e->getMessage(), 10);
      }
    }

    try {
      socket_connect($this->_socket, $address, $port);
    } catch (\Exception $e) {
      throw new \Exception($e->getMessage(), 10);
    }

    return $this;
  }

  public function read(int $length = 2048)
  {
    if ($this->_socket === null) {
      throw new \Exception('Didn\'t define socket connection', 20);
    }

    return socket_read($this->_socket, $length);
  }

  public function write(string $buf)
  {
    if ($this->_socket === null) {
      throw new \Exception('Didn\'t define socket connection', 20);
    }

    socket_write($this->_socket, $buf, strlen($buf));

    return $this;
  }

  public function close()
  {
    if ($this->_socket === null) {
      throw new \Exception('Didn\'t define socket connection', 20);
    }

    socket_close($this->_socket);
    $this->_socket = null;

    return $this;
  }

  public function then(callable $func)
  {
    if ($this->_socket === null) {
      throw new \Exception('Didn\'t define socket connection', 20);
    }

    $func($this);

    return $this;
  }
}


```



# 使用实例

```php

   /**
     * 添加账号
     * @param $account
     * @throws \Exception
     */
    public function add($account)
    {
        $msg = "<xml><header><transcode>358001</transcode></header><body><actno>{$account}</actno><operType>0</operType></body></xml>";
        $msgStr = $this->getMsgStr($msg);
        $conn = $this->connect();
        Log::init(['type' => 'File', 'path' => ROOT_PATH . '/runtime/log/njbank/add']);
        Log::write("发送:" . $msgStr . "\n");
        $conn->write($msgStr);
        $getstr = "";
        // 解决 没有读完的问题
        while ($resp = $conn->read()) {
            $getstr .= $resp;
            if (strpos($resp, "<\xml>") === false) break;
        }
        $conn->close();
        Log::write("结束:" . $getstr . "\n");
        $resdata = self::decodeXml($getstr);
        if ($resdata['header']['code'] == '000000') {
            $data = [
                'res' => true,
                'message' => '成功'
            ];
        } else {
            $data = [
                'res' => false,
                'message' => $resdata['header']['message']
            ];
        }
        return $data;
    }
```