---
inclusion: auto
---

# 飞书文档读写操作规范

## 工具说明

本项目通过 `lark-cli` 命令行工具与飞书文档交互。lark-cli 已全局安装，用户身份已授权。

## 读取飞书文档

```bash
lark-cli docs +fetch --doc "<飞书链接或obj_token>" --api-version v2 --as user --format pretty
```

## 获取飞书 Wiki 节点信息

```bash
lark-cli wiki +node-get --node-token "<飞书链接或node_token>" --as user --format pretty
```

## 创建飞书文档节点

```bash
lark-cli wiki +node-create --title "<文档标题>" --parent-node-token "<父节点node_token>" --space-id "<space_id>" --as user
```

## 写入飞书文档内容

```bash
lark-cli docs +update --doc "<obj_token>" --as user --mode overwrite --markdown @./<临时文件名>.md
```

## 必须遵守的规则

1. **所有命令必须加 `--as user`**，不要用 bot 身份
2. **临时文件用完必须立即删除**，不要留在工作目录里
3. **临时文件使用相对路径**：`@./文件名.md`，不要用绝对路径
4. **不要修改用户的模版文档**，只能读取参考
5. **写入内容时用 `--mode overwrite`**（v1 API）
6. **cwd 必须设为 `/Users/liyingqi/Desktop/虾兵`**，确保临时文件路径正确

## 常用 space 和节点

- 用户自有 space：`7480720750190608385`
- 用户自有 space 下的备用父节点：`UWsfwXhnWiPVxUk1zWBctB6UnXe`
- 另一个 space（虾兵项目）：`7497785984860553218`
- PRD 备用父节点：`Qf36wn4aRig8DkkTIUFcToV2nnc`

## 操作流程示例

当用户要求读取或写入飞书文档时：

1. 用 `docs +fetch` 读取文档内容
2. 用 `wiki +node-get` 获取节点信息（node_token、space_id等）
3. 如需创建新文档：先 `wiki +node-create` 创建节点，获取 obj_token
4. 将内容写入本地临时文件（如 `tmp_content.md`）
5. 用 `docs +update` 将临时文件内容写入飞书
6. 删除临时文件

## 注意事项


---

# 飞书消息发送能力

## 功能说明

可以代替用户以用户身份在飞书群聊中发送消息。已授权 scope：`im:chat:read`、`im:message`、`im:message.send_as_user`。

## 搜索群聊

```bash
lark-cli im +chat-search --query "<群名关键词>" --as user
```

## 发送消息到群

```bash
lark-cli im +messages-send --chat-id "<chat_id>" --as user --text "<消息内容>"
```

## 发送消息给个人（通过 user_id）

```bash
lark-cli im +messages-send --user-id "<open_id>" --as user --text "<消息内容>"
```

## 列出群消息

```bash
lark-cli im +chat-messages-list --chat-id "<chat_id>" --as user
```

## 搜索消息

```bash
lark-cli im +messages-search --query "<关键词>" --as user
```

## 常用群 chat_id

- 李英奇的东海龙宫：`oc_383fc22540df1ab53b46eac460938794`

## 使用规则

1. **所有命令必须加 `--as user`**
2. **发消息前默认先让用户确认内容**，除非用户明确说"不用确认直接发"
3. **不要主动发消息**，只有用户明确要求时才发
4. **敏感内容（涉及人事、薪资、批评等）必须让用户确认后再发**

## 触发条件

当用户说"在XX群发消息"、"给XX发消息"、"帮我回复XX"时，按本流程执行。