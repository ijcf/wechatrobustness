# 微信应用增强鲁棒性实践建议
## 问题起因、现象和原因分析
- 有公众号运营者反馈，自己的公众号被使用者投诉。使用者声称，偶然出现自己的公众号账户中出现其他人的信息，包括其他人的隐私信息。
- 通过调查，在相关论坛中，从2020年8月开始，陆续有公众号运营者反馈自己的公众号有账号标识重复的现象。
<img width="775" height="262" alt="用户投诉" src="https://github.com/user-attachments/assets/fdde607f-9d4c-4697-9481-7d420bb63ce5" />

- 期间，微信官方提到，问题的原因和获取到虚拟的（快照的）openID有关，同时称应检查公众号运营者自己的代码，按照微信官方的最佳实践进行代码逻辑实现。
- 经分析，**在快照页模式下，openID有可能重复。不可将此openID作为用户的唯一标识**，否则，可能出现使用者所称的现象。
- 此外，2025年8月19日，官方确认，在某些情况下，微信某些版本可能触发相关问题，并进行了紧急修复。
<img width="806" height="535" alt="官方修复" src="https://github.com/user-attachments/assets/b2d17feb-e17c-4499-86b9-4f3094ce7e71" />

## 实践建议
为保证微信应用（包括不限于公众号、小程序等）最终使用者的隐私安全，在进行微信实践中建议采取如下措施：
1. 遵循**微信官方的最佳实践**。按照微信官方的最佳实践，对公众号获取完整授权的流程进行调整，需授权时再请求授权。相关实践指导，请参考：https://developers.weixin.qq.com/community/minihome/doc/000c2c34068880629ced91a2f56001
这里需特别注意is_snapshotuser的判断，将此字段作为后台业务流程，尤其是隐私数据的返回流程是否执行的关键。
2. 前后台**双重保障用户隐私**。后台获取的是否为快照的状态，传给前台，如果为快照模式，则只显示基础的信息，并停止后续业务接口API的请求，同时在前台以友好的提示信息，引导用户进行完整授权的操作。在提升用户体验的同时，主动保护用户数据。
3. 记录**异常访问的行为数据**。因前端脚本暴露在外部，且依赖于微信APP的鲁棒性，在微信APP出现非预期行为，或者有用户进行前端逻辑篡改攻击时，前端保护机制可能失效。但后端可检测到此类行为（如is_snapshotuser字段存在，且涉及隐私的业务流程API仍被调用）时，仍然停止执行后续业务逻辑来防止隐私数据泄露，同时重点记录此用户code、openID、IP、UA等信息，作为后期调查分析的依据。
## 参考引用
- [《openid怎么会重复？》](https://developers.weixin.qq.com/community/develop/doc/0002086137c620ca0cdaf3da351000?highLine=openid%25E9%2587%258D%25E5%25A4%258D)
- [《OpenID是否会重复》](https://developers.weixin.qq.com/community/develop/doc/000e2ebdd18c10df2d9a11a5e57000)
- [《同一个微信公众号，两个用户返回了相同的openid》](https://developers.weixin.qq.com/community/develop/doc/000242a1120418b052c37435766800?highLine=openid%25E9%2587%258D%25E5%25A4%258D)
- [《鸿蒙NEXT-SP2无法正常进行网页授权》](https://developers.weixin.qq.com/community/develop/doc/000826094f8bc0fe8ec3330c366400)
- [《网页授权不同微信用户获取到相同的openid》](https://developers.weixin.qq.com/community/develop/doc/000ee0ebad8d70cc4b83f16c661c00?highLine=openid%25E7%259B%25B8%25E5%2590%258C)
- [《微信鸿蒙版，不同微信号，openid重复？》](https://developers.weixin.qq.com/community/develop/doc/0006021751c0f8bd8cc38d88066800?highline=openid%20%E9%87%8D%E5%A4%8D)
- [《公众号H5网页未授权获取到虚拟openId重复？》](https://developers.weixin.qq.com/community/develop/doc/000c2ae9144900bccfd3c59b666c00?highLine=openid%25E9%2587%258D%25E5%25A4%258D)
- [《H5页面，用户授权后，通过Code换取用户授权 Token返回的OpenID重复》](https://developers.weixin.qq.com/community/develop/doc/000a2c8d718c50d6ebb3fc32761000?highLine=1.0.9%2520openid)
- [《快照模式这几天是否有问题？》](https://developers.weixin.qq.com/community/develop/doc/000cc63ab247a88c4ec35da7b66c00?highLine=%25E5%25BF%25AB%25E7%2585%25A7%25E6%25A8%25A1%25E5%25BC%258F)
- [《关于用户还未授权信息，微信快照页已经颁发了code？且code居然是另一个人的微信信息（严重bug）》](https://developers.weixin.qq.com/community/develop/doc/00086a53494880386fe377e4a6bc00?highLine=%25E5%25BF%25AB%25E7%2585%25A7%25E6%25A8%25A1%25E5%25BC%258F)
