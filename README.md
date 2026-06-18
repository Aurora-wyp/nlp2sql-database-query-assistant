# nlp2sql 数据库查询助手

基于 [Dify](https://dify.ai) 构建的智能 NL2SQL 应用，输入自然语言查询问题，自动生成 SQL 语句、执行数据库查询，并返回数据分析和可视化图表。

## 功能

- 自然语言转 SQL：根据 `student_grades` 表结构自动生成多条多维度 MySQL 查询语句
- 批量执行 SQL：迭代执行生成的 SQL 并汇总结果
- 智能分析：LLM 对查询结果进行数据分析，输出 Markdown 格式的结论
- 图表可视化：自动选择合适的图表类型（线性图 / 柱状图 / 饼状图）展示数据
- 支持多种输出格式：JSON、CSV、YAML、Markdown、Excel、HTML

## 工作流

```
用户输入 → LLM 生成 SQL → 代码提取 JSON → 迭代执行 SQL → LLM 汇总分析 → 条件分支生成图表
```

### 节点说明

| 节点 | 类型 | 说明 |
|------|------|------|
| 用户输入 | Start | 接收自然语言查询 |
| LLM | LLM (qwen3.6-plus) | 根据表结构生成 SQL 语句（JSON 数组） |
| 代码执行 | Code (Python) | 从 LLM 输出中提取 JSON |
| 迭代 | Iteration | 逐条执行 SQL |
| SQL Execute | Tool (database) | 在数据库中执行查询 |
| LLM 汇总 | LLM (qwen3.6-plus) | 分析结果并生成图表参数 |
| 代码执行 | Code (Python) | 提取 ECharts 参数 |
| 条件分支 | If-Else | 根据图表类型路由 |
| 线性图/柱状图/饼图 | Tool (echarts) | 生成对应图表 |

### 数据库表结构

**表名**: `student_grades` (学生成绩信息表)

| 字段 | 类型 | 说明 |
|------|------|------|
| id | 主键 | 自增 ID |
| student_id | - | 学号 |
| student_name | - | 学生姓名 |
| class_name | - | 班级 |
| subject | - | 科目 |
| score | - | 分数 |
| exam_date | - | 考试日期 |
| semester | - | 学期 |
| grade | - | 年级 |
| created_at | - | 记录创建时间 |
| updated_at | - | 记录更新时间 |

## 依赖插件

在 Dify 中导入前，需先在「插件」市场安装以下插件：

| 插件 | 版本 | 用途 |
|------|------|------|
| `langgenius/tongyi` | 0.1.48 | 通义千问大模型（qwen3.7-plus） |
| `hjlarry/database` | 0.0.6 | 数据库连接与 SQL 执行 |
| `langgenius/echarts` | 0.0.7 | ECharts 图表生成 |

## 导入方法

1. 登录 Dify 平台，进入「工作室」
2. 点击「导入应用」
3. 选择 `nlp2sql数据库查询助手.yml` 文件
4. 导入后配置：
   - **LLM 节点**：确认通义千问模型密钥已配置
   - **SQL Execute 节点**：配置数据库连接 URI（如 `mysql+pymysql://user:password@host:port/database`）
5. 发布应用即可使用

## 示例

**输入**: "查询学校各科目平均分情况"

**输出**:
- 各科目平均分数据表格
- 柱状图展示各科目平均分对比
- Markdown 格式的数据分析结论

## 技术栈

- [Dify](https://github.com/langgenius/dify) - LLM 应用开发平台
- 通义千问 (qwen3.6-plus) - 大语言模型
- ECharts - 数据可视化
- MySQL - 数据库
