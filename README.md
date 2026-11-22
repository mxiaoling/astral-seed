# astral-seed

知识库种子库。**只放数据，不放源码**——插件本身在另一个仓库里。

## 这是什么

`knowledge.db.gz` 是 Star Workshop 插件的离线知识索引，一个 gzip 压缩的 SQLite 数据库。

插件装上时不带任何语料。使用者在「研习室」里点「下载研习知识库」，插件从本仓库下载这个文件、
解压到 `~/.dsh/storages/astral-workshop/knowledge.db`，之后所有检索都在本机完成。

## 怎么拿到还原成 Markdown

数据库里**每个文件都以完整原文存着**，块只是一对偏移量。所以这份索引是可逆的：

```js
import { DatabaseSync } from 'node:sqlite'
import { mkdirSync, writeFileSync } from 'node:fs'
import { dirname, join } from 'node:path'

const db = new DatabaseSync('knowledge.db')
const rows = db.prepare('SELECT s.root AS source, d.rel_path AS path, d.text AS text FROM document d JOIN source s ON s.id = d.source_id').all()
for (const row of rows) {
  const folder = String(row.source).split(/[/\\]/).pop()
  const target = join('restored', folder, row.path)
  mkdirSync(dirname(target), { recursive: true })
  writeFileSync(target, row.text, 'utf8')
}
console.log(rows.length + ' files restored')
```

## 库里有什么

八个主题目录，744 个 Markdown 文件，按标题切块后约 23,514 块：

`advanced` · `database` · `design` · `math` · `programming` · `systems` · `teamwork` · `tech-strategy`

## 怎么用

在插件的「设置 → 星穹工坊 → 深空心核 → 种子库地址」里填这个文件的可下载地址，
或者直接填本地路径。留空则插件使用内置的默认地址。

## 更新

重新构建后覆盖 `knowledge.db.gz` 并提交即可。文件名保持固定，地址就不会变。

