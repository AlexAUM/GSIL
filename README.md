# GSIL(GitHub Sensitive Information Leak)
> Monitor Github sensitive information leaks in near real time and send alert notifications.

> 近实时监控Github敏感信息泄露，并发送告警通知。

## Installation(安装)
> 仅在Python3下验证过

```
git clone https://github.com/FeeiCN/gsil.git
cd gsil/
pip install -r requirements.txt
```

## Configuration(配置)
#### gsil/config.gsil: 告警邮箱和Github配置
```
[mail]
host : smtp.exmail.qq.com
port : 25
mails : gsil@domain.com
from : GSIL
password : your_password
to : feei@feei.cn

[github]
# 扫描到的是否立刻Clone到本地
clone: false

# Github Token用来调用相关API
# https://github.com/settings/tokens
tokens : your_token

```
#### gsil/rules.gsil: 扫描规则
> 规则一般选用内网独立的特征，比如蘑菇街的外网是mogujie.com，蘑菇街的内网是mogujie.org，则可以将mogujie.org作为一条规则。
> 其它还有类似代码头部特征、外部邮箱特征等

| 字段 | 意义 | 选填 | 默认 | 描述 |
| --- | --- | --- | --- | --- |
| keyword | 关键词 | 必填 | - | 多个关键词可以用空格，比如‘账号 密码’；某些关键字出现的结果非常多，所以需要精确搜索时可以用双引号括起来，比如‘”ele.me“’；|
| ext | 指定文件后缀 | 可选 | 全部后缀 | 多个后缀可以使用英文半角逗号（,）分隔，比如`java,php,python` |
| mode |  匹配模式 | 可选 | 正常匹配 | 正常匹配：匹配包含keyword的行，并记录该行附近行 / 仅匹配：仅匹配包含keyword行 / 全部匹配（不推荐使用）：搜出来的整个问题都算作结果 |

```
{
    # 一级分类，一般使用公司名，用作开启扫描的第一个参数（python gsil.py test）
    "test": {
        # 二级分类，一般使用产品线
        "mogujie.com": {
            # 公司内部域名
            "\"mogujie.org\"": {},
            # 公司代码特征
            "copyright meili inc": {},
            # 内部主机域名
            "yewu1.db.mogujie.host": {},
            # 外部邮箱
            "mail.mogujie.com": {}
        }
    }
}
```


## Usage(用法)

```
python gsil.py test
```

```bash
$ crontab -e

# 每个小时运行一次
0 * * * * /usr/bin/python /var/app/gsil/gsil.py test > /tmp/gsil
# 每天晚上11点发送统计报告
0 23 * * * /usr/bin/python /var/app/gsil/gsil.py --report
```
