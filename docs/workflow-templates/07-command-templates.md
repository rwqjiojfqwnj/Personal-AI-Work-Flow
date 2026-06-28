# 常用命令模板

> 用途：统一团队对 Comet、Impeccable、codegraph 的常用调用方式。

---

## 1. 主流程命令

```text
/comet-open
/comet-design
/comet-build
/comet-verify
/comet-archive
```

---

## 2. codegraph / understand 命令

```text
/understand --language zh --full
/understand --auto-update
/understand-dashboard
/understand-explain frontend/src/views/xxx.vue
/understand-diff
```

---

## 3. Impeccable 推荐命令

### 页面开始前

```text
/impeccable init
/impeccable shape [目标页面或区域]
```

### 页面完成后

```text
/impeccable critique [目标页面或区域]
/impeccable polish [目标页面或区域]
```

### 交付前

```text
/impeccable audit [目标页面或区域]
```

---

## 4. 阶段推荐组合

### 新需求进入

```text
/comet-open
/understand-diff
```

### 设计冻结前

```text
/comet-design
/impeccable shape [关键页面]
```

### 开发中

```text
/comet-build
/understand-explain [关键文件]
```

### 提测前

```text
/comet-verify
/impeccable audit [重点页面]
/understand-diff
```

---

## 5. 团队约定建议

- 接口变更先改 OpenAPI，再改代码
- 新页面默认基于 Element Plus 开发
- 重点页面在提测前至少执行一次 Impeccable critique 或 audit
- 大改动在验收前补一次图谱更新和影响分析
