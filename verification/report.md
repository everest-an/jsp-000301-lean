# JSP-000301 · 形式化验证报告

> **总体结论：验证通过（在所列复核级别与信任范围下）。**
>
> 对于 JSP-000301（孙宇晨奖题库，原题 "If two consecutive positive integers are powerful, must at least one be a perfect square?"），在证明仓库 `everest-an/jsp-000301-lean`（分支 `main`，完整 40 位 commit SHA 记录于提交 PR 正文）上，该提交**完整解决**了指定原题——它构造并核验了 Golomb (1970) 的反例对 12167、12168：两者连续、都 powerful、都不是完全平方，从而否定了全称命题。决定性理由：原样构建成功、目标公理审计为 `standard_axioms_only`（仅 `propext, Classical.choice, Quot.sound`）、无 `sorryAx`。

| 必答问题 | 明确判断 | 决定性依据 |
| --- | --- | --- |
| 证明对象是否就是指定原题？ | 是 | 原题为 yes/no 全称问题；`jsp_000301` 否定该全称命题，反例满足全部前提（连续、>0、两者 powerful）并否定结论（两者皆非平方） |
| 指定 commit 是否实际验证通过？ | 是 | `lake build +Jsp000301` 退出码 0；`lake env lean Jsp000301.lean` 退出码 0；审计文件 `#print axioms` 退出码 0 |
| 是否完整解决原题？ | 是 | 单个满足前提且违反结论的反例即完整反证；`powerful_12167/12168`、`not_square_12167/12168` 全部无缺口 |
| 是否满足本次验证的 Lean 完整性要求？ | 满足 | 全部目标机械检查通过、仅标准公理、无 `sorry`/自定义公理/本机计算公理 |

**复核级别**：实际 Lean 检查（源码检查 + 目标构建 + `#print axioms`）。**未执行**：kernel replay、独立 challenge + comparator 外部检查（checker 兼容性未探测）。**扩展信任**：无（仅 Lean 标准三公理）。**适用范围**：本结论只涉及 Lean 证明完整性，不决定合并、授奖或支付。

---

## 固定证据

- 报告生成时间：2026-09-21（Australia/Sydney）。
- 题库来源：`TheJustinSunPrize/awards`，`problems/catalog-0301-0400.md#JSP-000301`（本地快照 `_ref-jsp-catalog/catalog-0301-0400.md`）。
- JSP 编号：`JSP-000301`；原始题号：Erdős Problems #365（题库 attribution 链接 `https://www.erdosproblems.com/latex/365`）。
- 原始数学来源：S. W. Golomb, *Powerful numbers*, Amer. Math. Monthly **77**(8) (1970) 848–852；W. A. Webb, *Consecutive integer pairs of powerful numbers…*, Fibonacci Quart. (1976) 111–116。
- **Lean 证明仓库（公开）**：`https://github.com/everest-an/jsp-000301-lean`，分支 `main`。被核验的证明工件为 `Jsp000301.lean`，sha256 `1e2f2119545244c7d9e3c14c83b78435268513cf6cf4c3ad60f2bb912f993a35`；提交 PR 正文给出该文件所在提交的完整 40 位 SHA。
- 目标声明：`JSP000301.jsp_000301`（`Jsp000301.lean`）。
- 源码文件 sha256：`1e2f2119545244c7d9e3c14c83b78435268513cf6cf4c3ad60f2bb912f993a35`。
- `targets.json`：`_work/audit-jsp000301/targets.json`。
- 版本冲突：无。

> **审计执行方式说明。** 本次复核在本地隔离工作副本上执行，其 `Jsp000301.lean` 的 sha256 与上列提交源码**逐字节相同**（`1e2f2119…3a35`），工具链为提交固定的 `leanprover/lean4:v4.35.0-rc2`，mathlib 为提交 manifest 固定的 `0acbe13e167f256615da586346deba1c16fd1082`。本地以 `path` require 指向同一 mathlib revision（公开仓库以 `git` require 固定同一 revision）；获取机制不同，被核验的字节、工具链与依赖 revision 相同。

## 数学命题与覆盖

**原题精确陈述**（题库）："If two consecutive positive integers are powerful, must at least one be a perfect square?"

**Lean 目标声明**：
```lean
def Powerful (n : ℕ) : Prop := ∀ p : ℕ, p.Prime → p ∣ n → p ^ 2 ∣ n
def IsSquare (n : ℕ) : Prop := ∃ k : ℕ, k * k = n

theorem jsp_000301 :
    ¬ (∀ n : ℕ, 0 < n → Powerful n → Powerful (n + 1) →
        IsSquare n ∨ IsSquare (n + 1))
```

**覆盖矩阵**

| 原题要求 | 精确数学含义 | Lean 声明 | 对应关系 | 覆盖状态 |
| --- | --- | --- | --- | --- |
| "two consecutive positive integers" | `n, n+1`，`n > 0` | `h 12167 (by norm_num) …`，`n = 12167` | 一致 | 完整 |
| "powerful" | 每个素数 `p ∣ n` ⇒ `p² ∣ n` | `Powerful`（标准定义） | 等价 | 完整 |
| "perfect square" | `∃ k, k·k = n` | `IsSquare`（= mathlib `IsSquare` 于 `ℕ` 的定义） | 等价 | 完整 |
| "must at least one be a square?" | 全称命题 `∀n, … → IsSquare n ∨ IsSquare (n+1)` | `jsp_000301` 的否定 | 反例完整反证 | 完整 |
| 反例前提核验 | 12167、12168 皆 powerful | `powerful_12167`, `powerful_12168` | 已核验 | 完整 |
| 反例结论核验 | 12167、12168 皆非平方 | `not_square_12167`, `not_square_12168` | 已核验 | 完整 |

反例：12167 = 23³；12168 = 2³·3²·13² = 12167 + 1。两者皆 powerful（素数因子指数 ≥ 2），皆非完全平方（23 指数 3 为奇；2 指数 3 为奇）。110² = 12100 < 12167, 12168 < 12321 = 111²。

**语义要点**：原题为全称猜想，构造 1 个满足全部前提且违反结论的反例即为完整反证。已逐项核查前提（连续、正、两者 powerful）与否定结论（两者皆非平方）。未发现量词、对象、边界或定义上的错配。`Powerful`/`IsSquare` 采用标准定义，未影射同名标准概念、未把结论放进结构字段。

## 环境与执行

| 项目 | 实际值与证据 |
| --- | --- |
| OS/架构 | Windows (win32), x86_64-w64-windows-gnu |
| 工具链 | `lean-toolchain` = `leanprover/lean4:v4.35.0-rc2`；Lean 4.35.0-rc2，Lake 5.0.0 |
| 依赖 | mathlib4 rev `0acbe13e167f256615da586346deba1c16fd1082`（提交 manifest 固定） |
| 原样构建 | `lake build +Jsp000301`，cwd 项目根，退出码 0 |
| 显式目标检查 | `lake env lean Jsp000301.lean` 退出码 0（约 30 s） |
| 公理审计 | `#print axioms JSP000301.jsp_000301` → `[propext, Classical.choice, Quot.sound]` |
| kernel/外部复核 | 未运行（checker 兼容性 `not_probed`） |
| 运行后状态 | `inputs_stable = true`；HEAD 未变；无未跟踪源文件进入证明链 |

## 证明依赖与缺口

- `JSP000301.powerful_12167`：`[propext, Classical.choice, Quot.sound]`
- `JSP000301.powerful_12168`：同上
- `JSP000301.not_square_12167`：同上
- `JSP000301.not_square_12168`：同上
- `JSP000301.jsp_000301`：同上

全部为目标自身打印的传递公理，均为 Lean 标准基础，无 `sorryAx`、无自定义公理、无本机计算公理（未使用 `native_decide`）。

## 发现、限制和下一步

1. **未执行独立内核复核**（kernel replay / comparator）。观测：`checker_compatibility = not_probed`；影响：结论级别为"实际 Lean 检查"而非"独立内核复核"；最小补充：在受信隔离环境跑 kernel replay 与外部 checker。
2. **数学解题者署名未登记**。按奖项规则，JSP-000301 上架前已解，证明栏不发钱，仅形式化栏可领；且规则要求先登记解题者候选再登记 Lean 候选（"A Lean candidate cannot be registered without the prior solver record"）。提交时须先完成解题者/来源登记。
3. **本报告不推导奖项资格、作者身份、首创权或奖金决定。**

## 可复现产物

- 证明源：`Jsp000301.lean`（sha256 `1e2f2119…3a35`）
- 复现命令（仓库根）：
  ```
  lake exe cache get
  lake build +Jsp000301
  lake env lean Jsp000301.lean
  ```
- 公理审计：
  ```
  printf 'import Jsp000301\nset_option pp.all true\n#print axioms JSP000301.jsp_000301\n' > AuditTarget.lean
  lake env lean AuditTarget.lean
  ```
