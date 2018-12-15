
基于博客https://bcsec.org/index/detail/tag/2/id/407在复现权限校验不严格的漏洞时遇到的问题：

- 背景

代码在eosio.token.vul2文件夹中。
直接基于官方文档的token合约（https://github.com/EOSIO/eos/tree/master/contracts/eosio.token）的transfer函数修改的。
按照博客里的指示用kylin测试网作为实验环境，并使用js4eos（https://github.com/itleaks/js4eos）来进行创建账号、编译合约、部署合约、领取EOS、购买RAM等。
(测试账号名称:fortestatlas)
（问题1：）

- 执行过程

编译、部署、发币：

''''

js4eos compile -o eosio.token.vul2.wasm eosio.token.vul2.cpp
js4eos set contract fortestatlas ~/gadgets/eos/eosio/attack/eosio.token2.vul2
js4eos push action fortestatlas create '["fortestatlas", "10000000.0000 VULA"]' -p fortestatlas
js4eos push action fortestatlas issue '["fortestatlas", "10000000.0000 VULA"]' -p fortestatlas

''''

![编译和部署](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/complie%26deply.png)
![发币](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/create&issue.png)

问题2（abi文件）

正常转账：

![转账](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/transfer.png)

绕过权限转账转回来：

问题3：失败
![转账](https://github.com/AtlasQuan/Practice-of-EOS/blob/master/Pictures/transferback.png)




