# VISUAL TWIN CONTRACT
## Make the architecture visible, not merely described

Create a visual representation that mirrors the final architecture.

Do not force a predetermined diagram set if your architecture needs a better one.

---

## 1. Minimum visual understanding

A future reviewer should be able to see:
- the entire product at a glance;
- major component relationships;
- runtime data/control flow;
- privilege boundaries;
- normal runtime;
- incident/abnormal runtime;
- failure and recovery;
- implementation dependency order;
- build/release flow.

---

## 2. Editable format

If Figma or another editable visual-design tool is available, create an editable board.

Otherwise use repository-friendly editable formats such as:
- Mermaid,
- PlantUML,
- Graphviz,
- SVG,
- or another suitable source format.

Do not provide only screenshots.

---

## 3. Traceability

Every important visual entity must map to a stable project entity ID.

Create a visual traceability index mapping:

`visual node → component/mechanism → canonical document → implementation unit → tests/status`

A diagram is part of the knowledge system, not decoration.

---

## 4. Consistency

The final audit must verify:
- no important component exists only in diagrams;
- no important component exists only in text without a deliberate reason;
- names/IDs agree;
- privilege boundaries agree;
- runtime flows agree;
- failure/rollback flows agree.

---

## 5. Product/UI visualization

If the architecture includes a user-facing application or dashboard, visualize the important product flow sufficiently to show:
- main screens;
- system status;
- user decisions;
- experiment/safety states;
- recovery/error states;
- and how the UI maps to runtime components.

Do not spend disproportionate effort on visual polish before architecture truth is stable.
