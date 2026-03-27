> **版本**：1.0.0 | **状态**：draft | **更新时间**：2026-03-27T00:00:00Z
>
> **来源方案**：normal 场景 1.0.1 版本

---

#### 约定说明

本文档使用以下标准值：
- **布局类型**: `list` / `detail` / `form` / `dashboard` / `steps` / `custom`
- **区块类型**: `table` / `form` / `card` / `cards` / `chart` / `tabs` / `steps` / `timeline` / `description` / `statistic` / `custom`
- **字段类型**: `text` / `textarea` / `number` / `money` / `percent` / `date` / `datetime` / `daterange` / `time` / `select` / `multiselect` / `radio` / `checkbox` / `switch` / `upload` / `image` / `richtext` / `cascader` / `treeselect` / `user` / `department` / `address` / `phone` / `email` / `idcard` / `url` / `color` / `rate` / `slider` / `custom`
- **列类型**: `text` / `number` / `money` / `date` / `datetime` / `tag` / `status` / `avatar` / `image` / `link` / `progress` / `action`
- **操作位置**: `toolbar` / `toolbar-left` / `toolbar-right` / `row` / `row-more` / `form-footer` / `card-header` / `card-footer`
- **操作行为**: `navigate` / `modal` / `drawer` / `action` / `download` / `print`

---

## 一、产品概述

### 1.1 项目背景

长机科技做齿轮加工机床，设备管理现在主要靠老工程师的经验。没有统一的设备台账，预防性维护计划也做不起来，车间设备运行状态看不到，故障发现全靠人工打电话报修。

这次建设EAM系统，核心是两件事：把设备台账理清楚，让设备运行状态在监控大屏上看得见。

### 1.2 产品目标

- 建立设备全生命周期电子档案，实现设备信息可追溯
- 从被动维修转向预防性维护，降低意外停机率
- 实现维修工单全流程闭环管理，提高响应效率
- 通过监控大屏实时展示设备运行状态，支持快速决策
- 打通西门子数控系统数据，支持预测性维护

### 1.3 目标用户

| 角色 | 描述 | 核心诉求 |
|------|------|----------|
| 设备管理员 | 负责设备台账管理、维护计划制定 | 统一管理设备档案，制定和执行维护计划 |
| 维修工程师 | 执行维修工单，处理设备故障 | 快速接收工单，查询设备历史，记录维修过程 |
| 车间主管 | 监控车间设备运行状态 | 实时掌握设备状态，快速响应异常 |
| 仓库管理员 | 管理备品备件库存 | 关联设备BOM，安全库存预警 |
| 管理层 | 查看设备绩效和分析报表 | 多维度分析设备OEE、故障率、维护成本 |

### 1.4 范围定义

#### 本期包含

- 设备台账管理（设备档案、规格参数、维护历史）
- 预防性维护（维护计划、工单自动触发）
- 维修工单管理（创建、派工、执行、验收、归档）
- 备品备件管理（库存台账、BOM关联、安全库存预警）
- 监控大屏（设备状态、OEE指标、故障预警）
- 西门子系统集成（OPC UA/Modbus协议对接）
- 分析报表（设备OEE、故障率、维护成本）

#### 本期不含

- 移动端应用（P3需求，本期不做，客户未明确要求）
- 状态监测预警（P2需求，基于IoT传感器的预测性维护，本期不做，客户重点是监控大屏展示已有数据）
- 采购管理（备件采购流程不在本期范围，仅做库存预警）
- 多工厂管理（客户为单工厂，无需多工厂架构）
- 设备租赁管理（客户资产自用，无租赁业务）

---

## 二、信息架构

### 2.1 站点地图

```
📊 监控大屏 (/monitor)
📋 设备管理
  ├── 设备台账 (/equipment)
  ├── 维护计划 (/maintenance-plans)
  └── 维修工单 (/work-orders)
📦 备件管理
  ├── 备件台账 (/spare-parts)
  └── 库存预警 (/stock-alerts)
📈 分析报表 (/analytics)
⚙️ 系统设置
  ├── 基础数据 (/settings/master-data)
  └── 系统集成 (/settings/integration)
```

### 2.2 导航结构

| 一级菜单 | 二级菜单 | 路由 | 说明 |
|----------|----------|------|------|
| 监控大屏 | - | /monitor | 实时展示设备运行状态、OEE指标、故障预警 |
| 设备管理 | 设备台账 | /equipment | 设备全生命周期档案管理 |
| 设备管理 | 维护计划 | /maintenance-plans | 预防性维护计划制定和管理 |
| 设备管理 | 维修工单 | /work-orders | 维修工单全流程管理 |
| 备件管理 | 备件台账 | /spare-parts | 备品备件库存台账管理 |
| 备件管理 | 库存预警 | /stock-alerts | 备件安全库存预警列表 |
| 分析报表 | - | /analytics | 设备OEE、故障率、维护成本分析 |
| 系统设置 | 基础数据 | /settings/master-data | 设备分类、故障类型等基础数据配置 |
| 系统设置 | 系统集成 | /settings/integration | 西门子系统集成配置 |

---

## 三、功能模块

### 3.1 监控大屏

> 解决设备状态不可见问题，实时展示全局设备运行状态、OEE指标、故障预警，支持管理层快速决策。

#### 3.1.1 监控大屏首页

**路由**：`/monitor`
**布局**：`dashboard`
**描述**：实时监控设备运行状态的全屏展示界面

##### 全局状态区（statistic）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 设备总数 | totalEquipment | number | 全厂设备总数 |
| 运行中 | runningCount | number | 当前运行设备数 |
| 停机 | stoppedCount | number | 当前停机设备数 |
| 故障 | faultCount | number | 当前故障设备数 |
| 综合OEE | overallOEE | percent | 全厂综合设备效率 |

##### 分区状态区（chart）

| 字段 | fieldKey | 图表类型 | 说明 |
|------|----------|----------|------|
| 车间设备分布 | workshopDistribution | pie | 按车间展示设备分布 |
| 设备运行状态 | equipmentStatus | bar | 按车间展示运行/停机/故障数量 |

##### 关键指标区（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 排名 | rank | number | 否 | 固定显示TOP5 |
| 设备名称 | equipmentName | text | 否 | 设备名称 |
| 指标值 | metricValue | number | 否 | 故障次数/OEE值 |
| 趋势 | trend | text | 否 | 较上期变化 |

##### 实时预警区（timeline）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 预警时间 | alertTime | datetime | 预警发生时间 |
| 预警级别 | alertLevel | tag | 预警级别（紧急/警告/提示） |
| 设备名称 | equipmentName | text | 关联设备 |
| 预警内容 | alertContent | text | 预警描述 |

##### 趋势分析区（chart）

| 字段 | fieldKey | 图表类型 | 说明 |
|------|----------|----------|------|
| OEE趋势 | oeeTrend | line | 近30天OEE趋势 |
| 故障率趋势 | faultRateTrend | line | 近30天故障率趋势 |
| 维护成本趋势 | maintenanceCostTrend | line | 近12个月维护成本趋势 |

##### 业务规则

- 数据刷新频率：5秒
- 异常状态红色闪烁告警
- 支持自定义布局和指标配置
- 支持55寸到98寸大屏，分辨率自适应

---

### 3.2 设备管理

#### 3.2.1 设备台账列表

**路由**：`/equipment`
**布局**：`list`
**描述**：设备档案列表查询和管理

##### 筛选区（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 设备编号 | equipmentCode | text | 否 | 精确查询 |
| 设备名称 | equipmentName | text | 否 | 模糊查询 |
| 设备分类 | equipmentCategory | select | 否 | 选项来源: dict-equipment-category |
| 车间 | workshop | select | 否 | 选项来源: dict-workshop |
| 设备状态 | status | select | 否 | 选项来源: dict-equipment-status |

##### 数据表格（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 设备编号 | equipmentCode | text | 是 | 唯一标识 |
| 设备名称 | equipmentName | text | 是 | 设备名称 |
| 设备分类 | equipmentCategory | tag | 是 | 设备分类 |
| 车间 | workshop | tag | 是 | 所属车间 |
| 设备状态 | status | status | 是 | 运行状态 |
| 购置日期 | purchaseDate | date | 是 | 设备购置日期 |
| 操作 | actions | action | 否 | 查看/编辑 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 新增设备 | primary | toolbar-right | navigate → /equipment/create |
| 批量导入 | default | toolbar-right | modal → 导入弹窗 |
| 查看 | link | row | navigate → /equipment/:id |
| 编辑 | link | row | navigate → /equipment/:id/edit |

##### 业务规则

- 设备编号全局唯一
- 支持Excel批量导入

#### 3.2.2 设备台账详情

**路由**：`/equipment/:id`
**布局**：`detail`
**描述**：设备档案详细信息展示

##### 基础信息（description）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 设备编号 | equipmentCode | text | 唯一标识 |
| 设备名称 | equipmentName | text | 设备名称 |
| 设备分类 | equipmentCategory | text | 设备分类 |
| 车间 | workshop | text | 所属车间 |
| 设备状态 | status | status | 当前运行状态 |
| 购置日期 | purchaseDate | date | 设备购置日期 |
| 供应商 | supplier | text | 设备供应商 |
| 规格型号 | model | text | 规格型号 |
| 序列号 | serialNumber | text | 出厂序列号 |

##### 规格参数（card）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 额定功率 | ratedPower | number | 额定功率（kW） |
| 额定电压 | ratedVoltage | number | 额定电压（V） |
| 额定电流 | ratedCurrent | number | 额定电流（A） |
| 额定转速 | ratedSpeed | number | 额定转速（rpm） |

##### 维护历史（timeline）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 维护日期 | maintenanceDate | date | 维护完成日期 |
| 维护类型 | maintenanceType | tag | 维护类型（预防性/故障性） |
| 维护内容 | maintenanceContent | text | 维护内容描述 |
| 执行人 | executor | text | 执行人 |

##### 关联备件（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 备件编号 | partCode | text | 是 | 备件编号 |
| 备件名称 | partName | text | 是 | 备件名称 |
| 规格 | specification | text | 否 | 规格型号 |
| 安全库存 | safetyStock | number | 否 | 安全库存数量 |
| 当前库存 | currentStock | number | 否 | 当前库存数量 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 编辑 | primary | card-header | navigate → /equipment/:id/edit |
| 删除 | danger | card-header | action → 确认后删除 |

#### 3.2.3 设备台账新增/编辑

**路由**：`/equipment/create` / `/equipment/:id/edit`
**布局**：`form`
**描述**：新增或编辑设备档案

##### 基础信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 设备编号 | equipmentCode | text | 是 | 唯一标识，编辑时不可修改 |
| 设备名称 | equipmentName | text | 是 | 设备名称 |
| 设备分类 | equipmentCategory | select | 是 | 选项来源: dict-equipment-category |
| 车间 | workshop | select | 是 | 选项来源: dict-workshop |
| 购置日期 | purchaseDate | date | 是 | 设备购置日期 |
| 供应商 | supplier | text | 否 | 设备供应商 |
| 规格型号 | model | text | 否 | 规格型号 |
| 序列号 | serialNumber | text | 否 | 出厂序列号 |

##### 规格参数（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 额定功率 | ratedPower | number | 否 | 额定功率（kW） |
| 额定电压 | ratedVoltage | number | 否 | 额定电压（V） |
| 额定电流 | ratedCurrent | number | 否 | 额定电流（A） |
| 额定转速 | ratedSpeed | number | 否 | 额定转速（rpm） |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 保存 | primary | form-footer | action → 保存后返回列表 |
| 取消 | default | form-footer | navigate → 返回上一页 |

##### 业务规则

- 设备编号全局唯一，新增时自动校验
- 编辑时设备编号不可修改

---

#### 3.2.4 维护计划列表

**路由**：`/maintenance-plans`
**布局**：`list`
**描述**：预防性维护计划管理

##### 筛选区（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 设备名称 | equipmentName | text | 否 | 模糊查询 |
| 计划状态 | planStatus | select | 否 | 选项来源: dict-plan-status |
| 计划类型 | planType | select | 否 | 选项来源: dict-plan-type |

##### 数据表格（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 计划编号 | planCode | text | 是 | 唯一标识 |
| 计划名称 | planName | text | 是 | 计划名称 |
| 设备名称 | equipmentName | text | 是 | 关联设备 |
| 计划类型 | planType | tag | 是 | 计划类型 |
| 维护周期 | maintenanceCycle | text | 是 | 维护周期（日/周/月/年） |
| 下次执行日期 | nextExecutionDate | date | 是 | 下次执行日期 |
| 计划状态 | planStatus | status | 是 | 计划状态 |
| 操作 | actions | action | 否 | 查看/编辑/禁用 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 新增计划 | primary | toolbar-right | navigate → /maintenance-plans/create |
| 批量启用 | default | toolbar-left | action → 批量启用选中计划 |
| 批量禁用 | default | toolbar-left | action → 批量禁用选中计划 |
| 查看 | link | row | navigate → /maintenance-plans/:id |
| 编辑 | link | row | navigate → /maintenance-plans/:id/edit |

##### 业务规则

- 计划状态为"启用"时自动生成工单
- 工单生成时间基于维护周期和上次执行日期

#### 3.2.5 维护计划详情

**路由**：`/maintenance-plans/:id`
**布局**：`detail`
**描述**：维护计划详细信息展示

##### 计划信息（description）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 计划编号 | planCode | text | 唯一标识 |
| 计划名称 | planName | text | 计划名称 |
| 设备名称 | equipmentName | text | 关联设备 |
| 计划类型 | planType | tag | 计划类型 |
| 维护周期 | maintenanceCycle | text | 维护周期 |
| 计划状态 | planStatus | status | 计划状态 |
| 创建时间 | createTime | datetime | 创建时间 |

##### 维护内容（card）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 维护项目 | maintenanceItems | richtext | 维护项目清单 |
| 注意事项 | precautions | richtext | 维护注意事项 |

##### 执行历史（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 工单编号 | workOrderCode | link | 是 | 关联工单 |
| 执行日期 | executionDate | date | 是 | 执行完成日期 |
| 执行人 | executor | text | 否 | 执行人 |
| 执行结果 | executionResult | tag | 否 | 执行结果 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 编辑 | primary | card-header | navigate → /maintenance-plans/:id/edit |
| 禁用/启用 | default | card-header | action → 切换计划状态 |

#### 3.2.6 维护计划新增/编辑

**路由**：`/maintenance-plans/create` / `/maintenance-plans/:id/edit`
**布局**：`form`
**描述**：新增或编辑维护计划

##### 基础信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 计划名称 | planName | text | 是 | 计划名称 |
| 设备 | equipmentId | select | 是 | 选项来源: 设备台账 |
| 计划类型 | planType | select | 是 | 选项来源: dict-plan-type |
| 维护周期 | maintenanceCycle | select | 是 | 选项来源: dict-maintenance-cycle |
| 周期值 | cycleValue | number | 是 | 周期数值（如每3天） |
| 首次执行日期 | firstExecutionDate | date | 是 | 首次执行日期 |
| 计划状态 | planStatus | switch | 否 | 是否启用 |

##### 维护内容（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 维护项目 | maintenanceItems | richtext | 是 | 维护项目清单 |
| 注意事项 | precautions | richtext | 否 | 维护注意事项 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 保存 | primary | form-footer | action → 保存后返回列表 |
| 取消 | default | form-footer | navigate → 返回上一页 |

##### 业务规则

- 周期值必须大于0
- 首次执行日期不能早于当前日期

---

#### 3.2.7 维修工单列表

**路由**：`/work-orders`
**布局**：`list`
**描述**：维修工单查询和管理

##### 筛选区（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 工单编号 | workOrderCode | text | 否 | 精确查询 |
| 设备名称 | equipmentName | text | 否 | 模糊查询 |
| 工单状态 | workOrderStatus | select | 否 | 选项来源: dict-work-order-status |
| 工单类型 | workOrderType | select | 否 | 选项来源: dict-work-order-type |
| 紧急程度 | urgency | select | 否 | 选项来源: dict-urgency |
| 创建时间 | createTime | daterange | 否 | 时间范围查询 |

##### 数据表格（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 工单编号 | workOrderCode | text | 是 | 唯一标识 |
| 设备名称 | equipmentName | text | 是 | 关联设备 |
| 工单类型 | workOrderType | tag | 是 | 工单类型 |
| 故障描述 | faultDescription | text | 是 | 故障描述摘要 |
| 紧急程度 | urgency | tag | 是 | 紧急程度 |
| 当前状态 | workOrderStatus | status | 是 | 工单状态 |
| 指派给 | assignee | text | 是 | 当前处理人 |
| 创建时间 | createTime | datetime | 是 | 工单创建时间 |
| 操作 | actions | action | 否 | 查看/处理 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 新增工单 | primary | toolbar-right | navigate → /work-orders/create |
| 我的工单 | default | toolbar-left | action → 筛选当前用户工单 |
| 查看 | link | row | navigate → /work-orders/:id |
| 处理 | link | row | navigate → /work-orders/:id/process |

##### 业务规则

- 工单编号自动生成，格式：WO+年月日+4位序号
- 工单状态流转：待派工→已派工→处理中→待验收→已完成→已关闭

#### 3.2.8 维修工单详情

**路由**：`/work-orders/:id`
**布局**：`detail`
**描述**：维修工单详细信息展示

##### 工单信息（description）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 工单编号 | workOrderCode | text | 唯一标识 |
| 设备名称 | equipmentName | text | 关联设备 |
| 工单类型 | workOrderType | tag | 工单类型 |
| 故障描述 | faultDescription | text | 故障描述 |
| 紧急程度 | urgency | tag | 紧急程度 |
| 当前状态 | workOrderStatus | status | 工单状态 |
| 创建人 | creator | text | 创建人 |
| 创建时间 | createTime | datetime | 创建时间 |

##### 处理信息（card）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 指派给 | assignee | text | 当前处理人 |
| 开始时间 | startTime | datetime | 开始处理时间 |
| 完成时间 | completeTime | datetime | 完成时间 |
| 故障原因 | faultCause | text | 故障原因分析 |
| 处理措施 | treatment | text | 处理措施 |
| 更换备件 | replacedParts | text | 更换的备件 |
| 实际工时 | actualHours | number | 实际维修工时 |

##### 处理记录（timeline）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 操作时间 | operationTime | datetime | 操作时间 |
| 操作类型 | operationType | tag | 操作类型（创建/派工/接单/完成/验收） |
| 操作人 | operator | text | 操作人 |
| 备注 | remark | text | 操作备注 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 派工 | primary | card-header | modal → 派工弹窗（状态为待派工时显示） |
| 接单 | primary | card-header | action → 当前用户接单（状态为已派工时显示） |
| 处理 | primary | card-header | navigate → /work-orders/:id/process（状态为处理中时显示） |
| 验收 | primary | card-header | modal → 验收弹窗（状态为待验收时显示） |
| 关闭 | danger | card-header | action → 关闭工单（状态为已完成时显示） |

#### 3.2.9 维修工单新增

**路由**：`/work-orders/create`
**布局**：`form`
**描述**：新增维修工单

##### 基础信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 设备 | equipmentId | select | 是 | 选项来源: 设备台账 |
| 工单类型 | workOrderType | select | 是 | 选项来源: dict-work-order-type |
| 紧急程度 | urgency | select | 是 | 选项来源: dict-urgency |
| 故障描述 | faultDescription | textarea | 是 | 故障描述 |
| 故障发生时间 | faultTime | datetime | 是 | 故障发生时间 |
| 附件 | attachments | upload | 否 | 现场照片、文件等 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 提交 | primary | form-footer | action → 提交后返回列表，状态为待派工 |
| 取消 | default | form-footer | navigate → 返回上一页 |

##### 业务规则

- 新增工单默认状态为"待派工"
- 创建人为当前登录用户

#### 3.2.10 维修工单处理

**路由**：`/work-orders/:id/process`
**布局**：`form`
**描述**：维修工程师处理工单

##### 处理信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 故障原因 | faultCause | textarea | 是 | 故障原因分析 |
| 处理措施 | treatment | textarea | 是 | 处理措施描述 |
| 更换备件 | replacedParts | textarea | 否 | 更换的备件清单 |
| 实际工时 | actualHours | number | 是 | 实际维修工时 |
| 处理备注 | processRemark | textarea | 否 | 其他处理说明 |
| 附件 | attachments | upload | 否 | 维修后照片、文件等 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 提交完成 | primary | form-footer | action → 提交后状态变为待验收 |
| 保存草稿 | default | form-footer | action → 保存草稿，不改变状态 |

##### 业务规则

- 只有工单状态为"处理中"时才能访问
- 提交完成后状态变为"待验收"

---

### 3.3 备件管理

#### 3.3.1 备件台账列表

**路由**：`/spare-parts`
**布局**：`list`
**描述**：备品备件库存台账管理

##### 筛选区（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 备件编号 | partCode | text | 否 | 精确查询 |
| 备件名称 | partName | text | 否 | 模糊查询 |
| 备件分类 | partCategory | select | 否 | 选项来源: dict-part-category |

##### 数据表格（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 备件编号 | partCode | text | 是 | 唯一标识 |
| 备件名称 | partName | text | 是 | 备件名称 |
| 备件分类 | partCategory | tag | 是 | 备件分类 |
| 规格 | specification | text | 是 | 规格型号 |
| 安全库存 | safetyStock | number | 是 | 安全库存数量 |
| 当前库存 | currentStock | number | 是 | 当前库存数量 |
| 库存状态 | stockStatus | status | 是 | 库存状态（正常/预警/缺货） |
| 操作 | actions | action | 否 | 查看/编辑 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 新增备件 | primary | toolbar-right | navigate → /spare-parts/create |
| 批量导入 | default | toolbar-right | modal → 导入弹窗 |
| 查看 | link | row | navigate → /spare-parts/:id |
| 编辑 | link | row | navigate → /spare-parts/:id/edit |
| 入库 | link | row | modal → 入库弹窗 |
| 出库 | link | row | modal → 出库弹窗 |

##### 业务规则

- 库存状态自动计算：当前库存<安全库存*0.5为缺货，当前库存<安全库存为预警
- 备件编号全局唯一

#### 3.3.2 备件台账详情

**路由**：`/spare-parts/:id`
**布局**：`detail`
**描述**：备件详细信息展示

##### 基础信息（description）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 备件编号 | partCode | text | 唯一标识 |
| 备件名称 | partName | text | 备件名称 |
| 备件分类 | partCategory | text | 备件分类 |
| 规格 | specification | text | 规格型号 |
| 型号 | model | text | 型号 |
| 品牌 | brand | text | 品牌 |
| 单位 | unit | text | 计量单位 |
| 安全库存 | safetyStock | number | 安全库存数量 |
| 当前库存 | currentStock | number | 当前库存数量 |
| 库存状态 | stockStatus | status | 库存状态 |
| 供应商 | supplier | text | 供应商 |
| 参考价格 | referencePrice | money | 参考价格 |

##### 关联设备（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 设备编号 | equipmentCode | text | 是 | 设备编号 |
| 设备名称 | equipmentName | text | 是 | 设备名称 |
| 数量 | quantity | number | 否 | 单台设备使用数量 |

##### 出入库记录（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 操作时间 | operationTime | datetime | 是 | 操作时间 |
| 操作类型 | operationType | tag | 是 | 入库/出库 |
| 数量 | quantity | number | 是 | 数量 |
| 操作人 | operator | text | 否 | 操作人 |
| 备注 | remark | text | 否 | 备注 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 编辑 | primary | card-header | navigate → /spare-parts/:id/edit |
| 入库 | default | card-header | modal → 入库弹窗 |
| 出库 | default | card-header | modal → 出库弹窗 |

#### 3.3.3 备件台账新增/编辑

**路由**：`/spare-parts/create` / `/spare-parts/:id/edit`
**布局**：`form`
**描述**：新增或编辑备件台账

##### 基础信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 备件编号 | partCode | text | 是 | 唯一标识，编辑时不可修改 |
| 备件名称 | partName | text | 是 | 备件名称 |
| 备件分类 | partCategory | select | 是 | 选项来源: dict-part-category |
| 规格 | specification | text | 否 | 规格型号 |
| 型号 | model | text | 否 | 型号 |
| 品牌 | brand | text | 否 | 品牌 |
| 单位 | unit | text | 是 | 计量单位 |
| 安全库存 | safetyStock | number | 是 | 安全库存数量 |
| 供应商 | supplier | text | 否 | 供应商 |
| 参考价格 | referencePrice | money | 否 | 参考价格 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 保存 | primary | form-footer | action → 保存后返回列表 |
| 取消 | default | form-footer | navigate → 返回上一页 |

##### 业务规则

- 备件编号全局唯一，新增时自动校验
- 安全库存必须大于等于0

---

#### 3.3.4 库存预警列表

**路由**：`/stock-alerts`
**布局**：`list`
**描述**：备件安全库存预警列表

##### 数据表格（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 备件编号 | partCode | text | 是 | 唯一标识 |
| 备件名称 | partName | text | 是 | 备件名称 |
| 备件分类 | partCategory | tag | 是 | 备件分类 |
| 当前库存 | currentStock | number | 是 | 当前库存数量 |
| 安全库存 | safetyStock | number | 是 | 安全库存数量 |
| 预警级别 | alertLevel | tag | 是 | 预警级别（缺货/预警） |
| 操作 | actions | action | 否 | 入库 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 批量入库 | primary | toolbar-right | modal → 批量入库弹窗 |
| 入库 | link | row | modal → 入库弹窗 |

##### 业务规则

- 仅显示库存状态为"预警"或"缺货"的备件
- 当前库存<安全库存*0.5为缺货，当前库存<安全库存为预警

---

### 3.4 分析报表

#### 3.4.1 分析报表首页

**路由**：`/analytics`
**布局**：`dashboard`
**描述**：设备绩效分析报表

##### 筛选区（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 时间范围 | dateRange | daterange | 是 | 统计时间范围 |
| 车间 | workshop | select | 否 | 选项来源: dict-workshop |
| 设备分类 | equipmentCategory | select | 否 | 选项来源: dict-equipment-category |

##### OEE分析（chart）

| 字段 | fieldKey | 图表类型 | 说明 |
|------|----------|----------|------|
| OEE趋势 | oeeTrend | line | 按时间维度展示OEE趋势 |
| OEE对比 | oeeComparison | bar | 按车间/设备分类对比OEE |

##### 故障分析（chart）

| 字段 | fieldKey | 图表类型 | 说明 |
|------|----------|----------|------|
| 故障率趋势 | faultRateTrend | line | 按时间维度展示故障率趋势 |
| 故障类型分布 | faultTypeDistribution | pie | 故障类型占比分布 |
| TOP故障设备 | topFaultEquipments | bar | 故障次数最多的设备TOP10 |

##### 维护成本分析（chart）

| 字段 | fieldKey | 图表类型 | 说明 |
|------|----------|----------|------|
| 维护成本趋势 | maintenanceCostTrend | line | 按时间维度展示维护成本趋势 |
| 维护成本构成 | maintenanceCostComposition | pie | 预防性维护/故障性维护成本占比 |

##### 业务规则

- 时间范围默认最近30天
- 支持导出报表为Excel

---

### 3.5 系统设置

#### 3.5.1 基础数据配置

**路由**：`/settings/master-data`
**布局**：`tabs`
**描述**：基础数据配置管理

##### 设备分类（tabs - table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 分类名称 | categoryName | text | 是 | 分类名称 |
| 分类编码 | categoryCode | text | 是 | 分类编码 |
| 说明 | description | text | 否 | 说明 |
| 操作 | actions | action | 否 | 编辑/删除 |

##### 故障类型（tabs - table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 类型名称 | typeName | text | 是 | 类型名称 |
| 类型编码 | typeCode | text | 是 | 类型编码 |
| 说明 | description | text | 否 | 说明 |
| 操作 | actions | action | 否 | 编辑/删除 |

##### 车间（tabs - table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 车间名称 | workshopName | text | 是 | 车间名称 |
| 车间编码 | workshopCode | text | 是 | 车间编码 |
| 负责人 | manager | text | 否 | 负责人 |
| 操作 | actions | action | 否 | 编辑/删除 |

##### 备件分类（tabs - table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 分类名称 | categoryName | text | 是 | 分类名称 |
| 分类编码 | categoryCode | text | 是 | 分类编码 |
| 说明 | description | text | 否 | 说明 |
| 操作 | actions | action | 否 | 编辑/删除 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 新增 | primary | card-header | modal → 新增弹窗（按当前激活的tab） |

#### 3.5.2 系统集成配置

**路由**：`/settings/integration`
**布局**：`form`
**描述**：西门子数控系统集成配置

##### 集成配置（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 协议类型 | protocolType | select | 是 | 选项来源: dict-protocol-type |
| 服务器地址 | serverAddress | text | 是 | OPC UA/Modbus服务器地址 |
| 端口 | port | number | 是 | 服务器端口 |
| 节点ID | nodeId | text | 否 | OPC UA节点ID |
| 数据采集频率 | collectionFrequency | number | 是 | 数据采集频率（秒） |
| 启用状态 | enabled | switch | 是 | 是否启用 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 |
|------|------|------|------|
| 测试连接 | primary | form-footer | action → 测试连接是否成功 |
| 保存 | primary | form-footer | action → 保存配置 |
| 取消 | default | form-footer | navigate → 返回上一页 |

##### 业务规则

- 数据采集频率最小值为5秒
- 测试连接成功后才能保存

---

## 四、全局规则

### 4.1 角色权限

| 角色 | 描述 | 模块权限 |
|------|------|----------|
| 系统管理员 | 系统最高权限，负责系统配置和用户管理 | 全部模块全部权限 |
| 设备管理员 | 负责设备台账管理、维护计划制定 | 设备台账（查看/新增/编辑/删除）、维护计划（全部）、维修工单（查看/派工/验收）、备件管理（查看）、分析报表（查看） |
| 维修工程师 | 执行维修工单 | 设备台账（查看）、维修工单（查看/处理）、备件管理（查看/出库） |
| 车间主管 | 监控车间设备运行状态 | 监控大屏（查看）、设备台账（查看）、维修工单（查看/新增）、分析报表（查看） |
| 仓库管理员 | 管理备品备件库存 | 备件管理（全部）、设备台账（查看） |
| 普通用户 | 只读权限 | 监控大屏（查看）、分析报表（查看） |

### 4.2 数据字典

#### dict-equipment-status（设备状态）

| 值 | 显示 | 颜色 |
|----|------|------|
| running | 运行中 | success |
| stopped | 停机 | default |
| fault | 故障 | error |
| maintenance | 维护中 | warning |

#### dict-equipment-category（设备分类）

| 值 | 显示 | 颜色 |
|----|------|------|
| cnc | 数控机床 | default |
| milling | 铣床 | default |
| grinding | 磨床 | default |
| other | 其他设备 | default |

#### dict-plan-status（计划状态）

| 值 | 显示 | 颜色 |
|----|------|------|
| enabled | 启用 | success |
| disabled | 禁用 | default |

#### dict-plan-type（计划类型）

| 值 | 显示 | 颜色 |
|----|------|------|
| preventive | 预防性维护 | primary |
| corrective | 纠正性维护 | warning |

#### dict-maintenance-cycle（维护周期）

| 值 | 显示 | 颜色 |
|----|------|------|
| daily | 每日 | default |
| weekly | 每周 | default |
| monthly | 每月 | default |
| yearly | 每年 | default |

#### dict-work-order-status（工单状态）

| 值 | 显示 | 颜色 |
|----|------|------|
| pending | 待派工 | default |
| assigned | 已派工 | warning |
| processing | 处理中 | primary |
| pending_acceptance | 待验收 | warning |
| completed | 已完成 | success |
| closed | 已关闭 | default |

#### dict-work-order-type（工单类型）

| 值 | 显示 | 颜色 |
|----|------|------|
| breakdown | 故障维修 | error |
| preventive | 预防性维护 | primary |
| improvement | 改善性维护 | warning |

#### dict-urgency（紧急程度）

| 值 | 显示 | 颜色 |
|----|------|------|
| critical | 紧急 | error |
| high | 高 | warning |
| medium | 中 | primary |
| low | 低 | default |

#### dict-part-category（备件分类）

| 值 | 显示 | 颜色 |
|----|------|------|
| mechanical | 机械件 | default |
| electrical | 电气件 | default |
| hydraulic | 液压件 | default |
| consumable | 易耗件 | default |
| other | 其他 | default |

#### dict-stock-status（库存状态）

| 值 | 显示 | 颜色 |
|----|------|------|
| normal | 正常 | success |
| warning | 预警 | warning |
| shortage | 缺货 | error |

#### dict-workshop（车间）

| 值 | 显示 | 颜色 |
|----|------|------|
| workshop1 | 一车间 | default |
| workshop2 | 二车间 | default |
| workshop3 | 三车间 | default |

#### dict-protocol-type（协议类型）

| 值 | 显示 | 颜色 |
|----|------|------|
| opcua | OPC UA | default |
| modbus | Modbus | default |

### 4.3 状态流转

#### 维修工单状态流转

| 当前状态 | 操作 | 目标状态 | 条件 |
|----------|------|----------|------|
| 待派工 | 派工 | 已派工 | 指定处理人 |
| 已派工 | 接单 | 处理中 | 处理人接单 |
| 处理中 | 提交完成 | 待验收 | 填写处理信息 |
| 待验收 | 验收通过 | 已完成 | 验收人确认 |
| 待验收 | 验收不通过 | 处理中 | 验收人驳回 |
| 已完成 | 关闭 | 已关闭 | 任意时间可关闭 |

---

## 附录

### A. 变更记录

| 版本 | 日期 | 变更内容 |
|------|------|----------|
| 1.0.0 | 2026-03-27 | 初始版本，基于normal场景1.0.1方案生成 |
