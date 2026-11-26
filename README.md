# 大消息外置引用的交付担保与清理

payload 是可验证的合成字节身份，digest 以仿真一致性标签表示。copy 是某载荷位置和修订的不可复用身份。through 是已完成的半开区间尾端。

## 输入消息

每个 JSONL 文件是一段独立输入记录。每行包括整数秒点 at、消息名称 kind 与对象 record。at 代表中间件收到消息的时刻，同一秒内按行序提交。业务时间和对应回执引用写在 record 中。

- **payload**　payload/body/digest 为本地源上传的参照。
- **copy**　payload/copy/version/length/digest/until 的实物持有证明。
- **publish**　id/payload/groups；把固定载荷接入消息投递。
- **take**　id/group 请求 receipt。
- **progress**　receipt/group/copy/through，result 为 reading、complete 或 failed。 complete 必须是该组完整读取过所指内容的仿真证明。
- **offline**　copy 不可取。
- **rebind**　receipt 申请切换到另一 copy。 成功后只有仍有效的当前凭据可完成消费；旧回报另留历史。
- **release**　id/group 请求结束本组持有。
- **delete**　针对 copy 清理。
- **deleted**　载荷端确定已清理 copy。
- **inspect**　核对 copy 或 id。
- **restart**　代理重启。

## 示例

- partial-read-and-reference-relocation.jsonl
- two-publications-share-one-payload.jsonl

## 读取演练资料

运行环境为 Python 3.11，只使用标准库。在根目录运行以下命令可核对文件格式及读取接口。

    python3 scenario_reader.py examples
    python3 -m unittest discover -v

资料读取器会按文件统计各类参与方消息的数量。演练记录只描述各参与方交给系统的输入。
