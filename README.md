## 基于[一篇EOS漏洞分析的博客](https://bcsec.org/index/detail/tag/2/id/407) 在复现权限校验不严格的漏洞时遇到的问题：

代码在eosio.token.vul2文件夹中。直接基于[官方文档的token合约](https://github.com/EOSIO/eos/tree/master/contracts/eosio.token） 的transfer函数修改的。

按照博客里的指示用kylin测试网作为实验环境，并使用js4eos（https://github.com/itleaks/js4eos） 来进行创建账号、编译合约、部署合约、领取EOS、购买RAM等。(我的测试账号名称:fortestatlas)

> **问题1：在测试EOS合约漏洞时，在测试网上测试和在本地的私有节点上测试有什么区别和优劣之分吗？**

## 漏洞简单描述

没有使用require_auth( from )校验资产转出账户与调用账户是否一致。

## 执行过程

- 编译、部署、发币：

```

js4eos compile2 -o eosio.token.vul2.wasm eosio.token.vul2.cpp
js4eos set contract fortestatlas ~/gadgets/eos/eosio/attack/eosio.token2.vul2
js4eos push action fortestatlas create '["fortestatlas", "10000000.0000 VULA"]' -p fortestatlas
js4eos push action fortestatlas issue '["fortestatlas", "10000000.0000 VULA"]' -p fortestatlas

```

![编译和部署](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/complie%26deply.png)
![发币](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/create&issue.png)

> **问题2：根据js4eos的教程中[生成abi文件的命令](https://github.com/itleaks/js4eos#%E7%94%9F%E6%88%90abi%E6%96%87%E4%BB%B6generate-abi)，我不知道contractclass参数在这里应该怎么写，写“token”生成的abi文件是不正确的格式。最后我使用的abi文件是直接复制了通过cleos --abigen命令生成的abi文件。不确定这种做法会不会有什么问题。**

- 正常转账：

```
js4eos push action fortestatlas transfer '["fortestatlas", "kylinnum2222", "10000000.0000 VULA",""]' -p fortestatlas
js4eos get currency balance fortestatlas kylinnum2222
js4eos get currency balance fortestatlas fortestatlas
```

![转账](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/transfer.png)

- 绕过权限转账转回来：
```
js4eos push action fortestatlas transfer '["kylinnum2222", "fortestatlas", "10000000.0000 VULA",""]' -p fortestatlas
```

> **问题3：这里之前的运行都是符合预期的。但是这里利用漏洞绕过权限时执行失败了，不知道原因是什么。**

![转账](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/transferback.png)




