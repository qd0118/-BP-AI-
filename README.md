# 财务BP预算与经营分析案例

可复现的**模拟经营项目**，以安克创新 2024 年年报作为唯一公开财报校准来源。不是安克创新的内部系统、真实订单、实际预算或实际 2025 年结果，也不是格拉斯哥大学课程提交记录。当前项目制作时间以文件实际创建时间为准。

## 能做什么

- 2024 三类产品年度收入和营业成本与年报精确对齐；生成 2025 年 360 个产品×渠道×地区×月份的模拟预算和实际观察单元。
- SQLite 存储模拟订单汇总、费用和应收；视图 `vw_monthly_pnl` 用 SQL 做月度预算/实际关联。
- 月度收入拆为销量、组合结构和成交价格，成本拆为销量、结构和单位成本；12 个月逐月核对桥接恒等式。
- 模拟 9 月市场推广费提前投放，费用较模拟预算高 18%；Agent 依据阈值找异常并写附带数据源的报告。
- Streamlit 调整销量、售价、单位成本、预算在渠道之间的分配及收款延迟；展示利润和增量现金代理影响。
- Power BI 兼容 CSV 导出及 DAX 示例；因环境没有 Power BI Desktop，**不提供 `.pbix` 文件**。
- LLM 报告写作需用户自行设置 `OPENAI_API_KEY`；没有 API key 时可运行可核验的规则化报告，界面会如实标记未调用 LLM。

## 运行

Python 3.10+：

```bash
python -m venv .venv
source .venv/bin/activate                 # Windows: .venv\\Scripts\\activate
pip install -r requirements.txt
python -m app.build_data
python -m app.export_bi
python -m unittest discover -s tests -v
streamlit run app/dashboard.py
```

可选：设置 `OPENAI_API_KEY` 后，在网页中打开 LLM 报告开关；联网访问和 API 费用由使用者控制。仅发送模拟的汇总指标与情景结果，未向模型发送逐行订单。`OPENAI_MODEL` 可指定已获授权的兼容模型。

无需 Streamlit 也可直接运行前 4 条（需要 numpy、pandas），检查 `exports/sample_report_2025_09.txt`、`sample_evidence_2025_09.json` 和 Power BI 数据表。

## 财报口径与模型限制

公开来源：[安克创新 2024 年年度报告](https://disc.static.szse.cn/disc/disk03/finalpage/2025-04-28/9bd3ae70-7998-4fbe-99ff-87b7ebd43cb8.PDF)：第 22–23 页产品收入和成本、第 112 页合并利润表、第 180 页收入渠道说明。校准的三类产品合计收入 24,695,910,841.82 元、成本 13,914,543,034.34 元；与合并报表差额是“其他”产品/业务，未分配到本案例 SKU 明细。年报总收入 24,710,080,329.34 元、营业成本 13,918,403,694.15 元。

注意：年报按产品、渠道各自披露**边际总量**，没有披露产品×渠道×地区×月份的联合交叉分布。本案例中的该分布、单价、销量、费用部门、预算和回款节奏均为随机种子固定的模拟值。2025 年经营贡献是“收入−成本−所列费用”，并非公司披露的营业利润。增量现金代理只计算假设下收款变化和部分成本付现变化，**不是现金流量表预测**。预算调配的营销响应系数也是假设，供敏感性练习。

## 文件

- `app/build_data.py` 数据生成与入库；`data/source_manifest.json` 记录公开数和模拟范围。
- `app/analysis.py` SQL 取数、收入/成本归因及情景测算。
- `app/agent.py` 异常识别和报告生成 Agent；LLM 可选，失败时显式回退。
- `app/dashboard.py` Streamlit 交互看板。
- `app/export_bi.py` 导出 Power BI 所需事实表、案例报告和证据。
- `docs/power_bi.md` 建模、图表和 DAX 操作说明。
- `tests/test_finance.py` 公开数校准、桥接、利润和现金敏感性核对。

## 简历表述边界

可表述为“个人完成、基于 2024 年公开年报校准的模拟财务BP案例”。不得称“获取公司订单/ERP数据”“推动公司实际调整预算”或“2026 年格拉斯哥大学小组课程作业”，除非另有对应事实和交付证据。
