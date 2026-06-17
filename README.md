# Real-Time Stage-Aware Wildfire Progression Detection based on YOLOv11

> YOLOv11 기반 실시간 탐지 및 산불 성장 단계 분류 시스템

---

## 1. Overview

 본 프로젝트는 YOLOv11 기반의 실시간 산불 성장 단계 분류 시스템을 제안한다.

 기존의 화재 탐지 시스템은 화재 발생 여부를 판단하는 이진 분류(Binary Classification)에 주로 초점을 맞추고 있다. 그러나 이러한 방식은 실제 산불 대응 과정에서 필요한 위험도 평가와 상황 판단에 한계가 있다.

 본 연구에서는 화재의 크기와 연기의 특성을 활용하여 발화기(Incipient), 성장기(Growth), 최성기(Fully Developed), 쇠퇴기(Decay)의 4단계로 산불의 진행 상태를 분류한다. 이를 통해 단순 화재 탐지를 넘어 산불의 성장 과정을 실시간으로 모니터링하고, 보다 신속하고 효과적인 대응을 지원하는 단계 인식 기반(Stage-Aware) 산불 모니터링 시스템을 구현하였다.

### Key Features

- Real-time fire and smoke detection using YOLOv11
- Wildfire progression stage classification
- Event-based decision logic
- Black smoke recognition
- Real-time visualization and monitoring
- Lightweight and high-speed inference

### Demostration



---

## 2. Motivation

 기후 변화와 이상 기후 현상의 증가로 인해 산불은 전 세계적으로 더욱 빈번하고 대형화되는 추세를 보이고 있다. 이에 따라 산불을 조기에 탐지하고 위험도를 신속하게 평가할 수 있는 지능형 모니터링 기술의 필요성이 증가하고 있다.

 그러나 기존의 화재 탐지 시스템은 대부분 화재 발생 여부만을 판단하는 이진 분류 방식에 초점을 맞추고 있어, 실제 산불의 진행 상황을 파악하거나 대응 우선순위를 결정하는 데 한계가 존재한다.

 본 프로젝트는 화재의 크기와 연기의 특성을 활용하여 산불의 성장 단계를 추정하고, 이를 기반으로 보다 효과적인 위험도 평가와 실시간 산불 모니터링을 제공하는 것을 목표로 한다.

---

## 3. Challenges

### Domain Shift

 학습 데이터와 실제 산불 환경 간의 데이터 분포 차이로 인해 모델의 일반화 성능이 저하될 수 있다.

### Background Confusion

 구름, 안개, 햇빛 반사 및 밝은 배경과 같은 시각적 요소가 연기와 유사한 특징을 가져 오탐지의 원인이 될 수 있다.

### Scale Variation

 촬영 거리와 화재 규모에 따라 화염 및 연기의 크기가 크게 변화하므로 일관된 객체 탐지와 분류가 어려울 수 있다.

---

## 4. Pipeline

### Overall Framework

```text
Raw Images
→ Data Preprocessing
→ YOLOv11 Training
→ Fire & Smoke Detection
→ Event Generation
→ Wildfire Stage Classification
→ Visualization
```

### Wildfire Progression Stages

| Stage | Description |
|---|---|
| None | No fire detected |
| Incipient | Early ignition stage |
| Growth | Fire spreading stage |
| Fully Developed | Peak combustion stage |
| Decay | Declining stage |

---

## 5. Dataset

### Fire Dataset

- Roboflow Fire-Smoke Detection Dataset
- Multiple wildfire image sources

### Dataset Statistics

- Approximately 4,200 images
- Multiple domains
- 640 × 640 image resolution
- Train : Validation : Test = 7 : 2 : 1

### Class Definition

<table>
<tr>
<td valign="top">

<h4>Fire Classes</h4>

<table>
<tr><th>Class</th></tr>
<tr><td>Small Fire</td></tr>
<tr><td>Medium Fire</td></tr>
<tr><td>Large Fire</td></tr>
</table>

</td>

<td valign="top">

<h4>Smoke Classes</h4>

<table>
<tr><th>Class</th></tr>
<tr><td>Small Smoke</td></tr>
<tr><td>Large Smoke</td></tr>
<tr><td>Black Smoke</td></tr>
</table>

</td>
</tr>
</table>

---

## 6. Event Definition

산불 성장 단계는 탐지된 화염 및 연기 클래스의 조합을 기반으로 정의되며, <br>
이를 통해 발화기(Incipient), 성장기(Growth), 최성기(Fully Developed), 쇠퇴기(Decay)로 분류한다.

| Event Condition | Predicted Stage |
|---|---|
| None + No Fire | None |
| Small Fire OR Small Smoke | Incipient |
| Small Fire + Large Smoke | Growth |
| Large Fire | Fully Developed |
| Medium Fire + Black Smoke | Decay |
| Black Smoke Only | Decay |

---

## 7. Experimental Setup

<table>
<tr>
<td valign="top">

<h3>Hardware</h3>

<table>
<tr><th>Item</th><th>Value</th></tr>
<tr><td>GPU</td><td>NVIDIA A100 40GB</td></tr>
<tr><td>CUDA</td><td>12.6</td></tr>
<tr><td>PyTorch</td><td>2.8.0</td></tr>
</table>

</td>

<td valign="top">

<h3>Hyperparameters</h3>

<table>
<tr><th>Parameter</th><th>Value</th></tr>
<tr><td>Epochs</td><td>100</td></tr>
<tr><td>Batch Size</td><td>32</td></tr>
<tr><td>Learning Rate</td><td>0.0005</td></tr>
<tr><td>Optimizer</td><td>AdamW</td></tr>
</table>

</td>
</tr>
</table>

---

## 8. Results

<table>
<tr>
<td valign="top">

<h3>Detection Performance</h3>

<table>
<tr><th>Class</th><th>mAP@50</th></tr>
<tr><td>Large Fire</td><td>0.920</td></tr>
<tr><td>Medium Fire</td><td>0.743</td></tr>
<tr><td>Small Fire</td><td>0.827</td></tr>
<tr><td>Large Smoke</td><td>0.884</td></tr>
<tr><td>Small Smoke</td><td>0.754</td></tr>
<tr><td>Black Smoke</td><td>0.995</td></tr>
</table>

</td>

<td valign="top">

<h3>Real-Time Performance</h3>

<table>
<tr><th>Metric</th><th>Value</th></tr>
<tr><td>FPS</td><td>66–77</td></tr>
<tr><td>Inference Time</td><td>8.4–9.0 ms</td></tr>
</table>

</td>
</tr>
</table>

### Qualitative Results

- Fire size classification
- Smoke type recognition
- Wildfire progression stage prediction
- Real-time visualization

<!-- Insert result images here -->
