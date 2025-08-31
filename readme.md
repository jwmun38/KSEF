# 🌲 Wildfire AI Model & Soil Moisture Prediction Project

본 프로젝트는 **AI Hub**에서 제공한 `지역안전재난(산불) 방재의 고도화를 위한 대규모 인공지능 데이터베이스 구축` 모델을 기반으로,  
산불 탐지 및 토양 수분 예측 모델 개발 과정을 정리한 저장소입니다.

---

## 📂 Repository Structure
<br>

- `README.md` : 프로젝트 설명 및 사용 가이드
<br>

### Wildfire AI Model
- [`1.모델소스코드.zip`](https://drive.google.com/file/d/1_pSd7Gh8DGMqZEKRrANxaS2EdsJtmn28/view?usp=sharing) : MMDetection 기반 산불 탐지 모델 소스 코드 (압축본)
- [`2.학습모델파일.zip`](https://drive.google.com/file/d/1hxoNkZ0pqLI0WgRrAmWWQUp-p10z8P9G/view?usp=sharing) : 학습된 모델 가중치 파일 (`forest_fire.pth`)
- [`wildfire.zip`](https://drive.google.com/file/d/1P1KGEm729xhpiZaSeFVV0AeDGFlweC_a/view?usp=sharing) : Python 3.10 가상환경(venv) 압축본 (필요 패키지 포함) 


### Soil Moisture Prediction
- [`KSEF_토양수분예측_모델_Preprocessing.ipynb`](KSEF_토양수분예측_모델_Preprocessing.ipynb) : 데이터 수집 및 전처리 과정  
- [`KSEF_토양수분예측_모델_Modeling.ipynb`](KSEF_토양수분예측_모델_Modeling.ipynb) : 머신러닝/딥러닝 모델 설계 및 학습 코드  
- `KSEF_토양수분예측_모델_Evalutation.ipynb` : 모델 성능 평가 및 결과 분석  
- [`KSEF_데이터`](https://drive.google.com/drive/folders/1RwMc7uRIakQBg2vToZmek-hZ8aPIubFy?usp=sharing) : 기상청, 토양 수분 등 수집 데이터 & 모델 학습 결과 데이터 폴더 




---


## 🔥 Wildfire AI Model Usage

### 1) 가상환경 세팅
```bash
unzip wildfire.zip
source wildfire/bin/activate
````

### 2) 모델 실행

```bash
python demo/image_demo.py \
  demo/demo.jpg \
  restored_config.py \
  forest_fire.pth \
  --out result.jpg \
  --device cpu
```

* GPU 사용 시: `--device cuda:0`
* 출력: `result.jpg` 

예시 입력 이미지:
![demo image](demo.jpg)

실행 결과 예시:
![result image](result.jpg)


### 3) Troubleshooting

* `ModuleNotFoundError: mmdet/mmcv/torch` → venv 내 패키지 설치 확인
* `ImportError: mmcv._ext` → `mmcv-full` 재설치
* `RuntimeError: Numpy is not available` → `pip install "numpy<2"`
* 한글 라벨 깨짐 → matplotlib에 한글 폰트 등록 (`AppleGothic`)

---

## 🌱 Soil Moisture Model Development

* 공공데이터 기반 기상·토양 데이터를 수집

* 학습 모델 비교
  
| 계열 | 사용 모델 |
|------|-----------|
| **선형 계열** | Linear Regression, Ridge, Lasso, ElasticNet |
| **트리 계열** | RandomForest, GradientBoosting |
| **부스팅 계열** | XGBoost, LightGBM, CatBoost |


* 교차검증 및 평가 지표
- **교차검증 방식**: `TimeSeriesSplit(n=3)`  
- **평가지표**: MAE, RMSE, R²  
- **Baseline**: Persistence(전일값 유지) / 단순 평균 대비 개선율(스킬 스코어)  

* 깊이별 성능 결과
  
| 토양 깊이 | MAE | RMSE | R² | 비고 |
|-----------|------|------|-----|------|
| **10cm** | 2.72 | 3.37 | 0.75 | 안정적 |
| **20cm** | 2.99 | 3.71 | 0.81 | 가장 안정적 |
| **40cm** | 3.55 | 4.50 | 0.55 | 센서 고장·보간 영향 |
| **60cm** | 2.95 | 4.03 | 0.82 | 최고 성능 |
| **평균 (target_avg)** | 2.85 | 3.57 | 0.78 | 안정적 |

> 부스팅 계열(XGBoost, LightGBM, CatBoost)이 전반적으로 가장 높은 성능을 보였으며,  
> 20cm와 60cm 깊이에서 가장 높은 정확도를 보여주었다. 

* 변수 중요도 분석
- **Permutation Importance & SHAP 분석 결과**  
  - 기압(PA_MAX, PA_MIN, PA_MAVG) → **최상위 기여 변수**  
  - 강수량(단기 누적), 바람 평균, 기온, 이슬점온도도 중요한 변수  
- **Ablation Study**  
  - 기압 변수를 제외할 경우 성능이 급격히 붕괴  
  - → 기압이 단순 상관이 아닌 **핵심 예측 인자**임을 확인 / 추가 검증 필요  


* 모델 구조 및 분석 과정 :  [토양 수분 예측 프로젝트 문서](https://github.com/jwmun38/KSEF)
<br><br>
---

## 📖 Reference

* AI Hub: [지역안전재난(산불) 방재의 고도화를 위한 대규모 인공지능 데이터베이스 구축](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&dataSetSn=71330)
* OpenMMLab MMDetection: [https://github.com/open-mmlab/mmdetection](https://github.com/open-mmlab/mmdetection)
* Using soil moisture information to better understand and predict wildfire danger: a review of recent developments and outstanding questions: [https://doi.org/10.1071/WF22056](https://doi.org/10.1071/WF22056)
<br><br>
---




