
---

#### 0x01 上传至文档svn中的handover目录

每一个项目都有文档svn，用于交流项目相关的文档，比如echo项目的文档svn的名称为echo\_document。这个svn根目录下有一个handover目录（如果没有则创建），离职人员需要在handover目录中创建一个以自己姓名为名字的目录，将交接的文档资料上传到该目录下。比如，假定一个名为lixianmin的同学近期需要离职，则上传文档以后的基本目录结构应该是如下样子：

```
echo_document/handover/lixianmin/project1/*.*
             /other-project-documents
```

---

#### 0x02 交接文档原则

交接文档需要条款清晰，双方满意，相关的建议原则如下：

1. 路径清晰：在后续工作中，接手人发现相关问题时可以**按图索骥**、逐项检查，能很快的确定问题相关的资源、代码和人，然后决定解决办法。
2. **链接丰富**：尽可能将相关的文档都附上去，让你的交接文档成为你交接工作的索引文档。
3. 有**明确接手人**：一定要有明确的接手人，即使没有合适的也要指定一个，甚至是你的领导。
4. **抄送领导**：这个可能是最重要的步骤。
5. **验收评价**：无论接手人或者领导，都要验收评价你的交接文档，有反馈才标志着交接完成。注意，不是验收签字，而是验收评价，只有评价过了，才意味着对方真的看了你的文档，否则可能只是草草接收，其实对方并不清楚你写了啥。

---

#### 0x03 交接文档模板

是一个命名为handover.md或handover.docx的文档，用于说明项目需求的主要相关内容，要求每一个项目至少包含一份交接文档。

1. 对每一份完整的项目需求，给出：  
   1. 构架设计说明和设计思路（可图可文）。  
   2. 关键代码目录，以及主要代码文件的功能说明、职责划分。  
   3. 相关协议的功能说明。  
   4. 配置的格式、位置，配置项的功能性描述。

2. 接手人工作建议  
   1. **成功经验**传授：附加相关文档链接。  
   2. **失败经验**传授：**当前代码的设计缺陷，有哪些容易出错的坑及预防清单，未来可以预计改进的点及原因**，附相关文档链接。  
   3. 接手人需要学习技能教程或建议，附相关文档链接。  
   4. 与接手人沟通后增补的其它注意事项。

3. 接手人未来需要做的工作  
   1. 具体项目工作清单，按重要程度排序。  
   2. 未完成需求的详细说明，包含进一步的开发计划中相关内容及演变方向的说明。  
   3. 相关联系人（含策划、其它程序、UI、美术人员等），他们负责内容的清单。如果是接手了以前其它程序人员的工作，则把他以前写的交接文档等其它信息也包含进来。

4. 接手人文件与物品交付  
   1. 电子账户密码清单，包含但不限于git、svn、teambition、testin测试账号、URL网址等。  
   2. 电子资料统一上传公司服务器，附链接。  
   3. 如果有重要的纸质文档，除交接这些纸质文档外，另需扫描并上传文档图片，附链接。  
   4. 电脑按公司流程交接，其它硬件，比如测试机或外设，移交相关人员并签字（参考条目8：交接验收）。

5. 常见问题及应对方案  
   1. 问题一：无法找到某个文件？建议可先找 XXX 同事，如不能再找 XXX……  
   2. 问题二：具体项目如何操作？建议可找 XXX 同事，她之前操作过……  
   3. 问题三：……

6. 软件说明  
   1. 软件清单，含软件名、版本、plugins、SDK使用情况描述等。  
   3. 自己写的小工具程序（含exe和各类脚本）标注源代码的地址，并附功能和操作说明。

7. 后续联系方式  
   1. 手机：XXX  
   2. 邮箱：XXX  
   3. 微信：XXX

8. 交接验收清单

   | 项目 | 接手人签名 | 验收日期 | 验收评价 |
   | --- | --- | --- | --- |
   | 场景编辑器 | xxx | 2018-05-04 | XXXXXXX |
   | AI | xxx | 2018-05-04 | XXXXXXX |
   | 小米测试机 | xxx | 2018-05-04 |  |

---

#### 0x04 交接目录说明

每一个项目都需要有一个单独的目录存放相关文档，整个交接文档目录结构参考如下：

1. project1/
   * handover.md，本项目的交接文档，具体格式参考条目 《0x03 交接文档模板》
   * documents/\*.\*，自己写过的与项目相关的文档
2. project2/
   * ... \(参考project1\)
   * ...
3. other-documents/ （公司任职期间写过的跟具体项目无关的其它文档）

4. suggestions/  （对公司、项目、管理、执行、技术等各方面的建议或意见）

---

#### 0x05 References

1. [如何写好离职工作交接文档？](https://zhuanlan.zhihu.com/p/27434051)


