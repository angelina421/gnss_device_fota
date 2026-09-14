# LuatOS RTK GNSS MQTT 设备

## MQTT 主题

> **broker setting**
>
> `broker.hivemq.com:1883`
>
> `<imei>/down`：发送消息Topic
>
> **订阅消息**
>
> `<imei>/device_msg/up`：显示设备状态、命令结果
>
> `<imei>/position_msg/up`：发送GGA，供上位机解析当前位置
>
> * **`imei`**: 区分每一台设备，写在芯片丝印，如`862323080723805`
>
> * 所有上行消息统一使用 Unix 时间戳包装：
>
>   ```json
>   {
>       "timestamp":1789225283
>   }
>   ```

## MQTT 命令

### LED 控制

* `name` 
  * 红灯：`red`
  * 黄色：`yellow`
* `state`
  *  常亮： `on`
  * 闪烁 ：`blink`
  * 熄灭 ：`off`

```json
{
	"cmd":"led",
    "name":"red",
    "state":"blink"
}
```

### 查询电压

该命令时返回当前电压。

```json
{
    "cmd":"power"
}
```

### FOTA 升级

不填写 `version` 时请求最新版本，也可以指定版本号：

```json
{	
    "cmd":"fota",
 	"version":"v1.1"
}
```

### NTRIP 差分

连接最多重试 5 次，仍失败时通过 `device_msg/up` 上报最终错误。

```json
{
	"cmd":"ntrip",
    "host":"114.111.30.88",
    "port":8002,
    "user":"<user>",
    "password":"<password>",
    "mount":"RTCM33GRC"
}
```

### 语音播报

`volume` 范围为 0 到 100，可省略。

```json
{
    "cmd":"audio",
    "text":"测试播报",
    "volume":80
}
```

### 工作模式

* **启动休眠，关闭LED灯显示，GPIO电平降为1.8V，关闭GNSS模块**

  ```json
  {
      "cmd":"idle"
  }
  ```

* **启动唤醒，开启正常功能**

  ```json
  {
  	"cmd":"active"
  }
  ```

### 获取帮助

```json
{
    "cmd":"help"
}
```

## 设备信息与关机

设备联网启动后上报硬件和软件版本。硬件版本固定为 `v2.0`，软件版本使用配置中的 FOTA 版本号。

长按电源键关机时，设备会先向 `device_msg/up` 发布关机状态，再关闭电源保持。
