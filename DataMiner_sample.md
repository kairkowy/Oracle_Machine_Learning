
### Oracle Data Miner를 이용하여 Oracle Machine Learning 모델 만들기

----

```
작성자 : 고운용(kairkowy69@gmail.com), 작성일 : 2020. 5.8
```

##### 0. 예제 시나리오 및 개요
- ABC사(가상)의 고객 정보를 이용하여 보험을 구매할 가능성이 가장 높은 고객을 예측하는 예제입니다.
- Classification 분석 기법을 사용할 것이며 Classification 분석에 널리 쓰이는 GLM, SVM,DT 및 NB 알고리즘을 비교하고 예측능력이 좋다고 평가되는 Decision Tree(DT) 알고리즘으로 최종 결과를 도출하는 예제입니다.
- 사용 Machine Learning 분석 플랫폼은 Oracle DataMiner, Oracle Database 19c를 사용합니다.
- Oracle DataMiner는 Oracle Sqldeveloper에 포함되어 있는 기능으로 Oracle Machine Learning을 위한 GUI 툴입니다. 특히 데이터 시각화, ML 분석 프로세스 자동화 기능 등을 포함하고 있습니다.
- Oracle Machine Learning은 오라클 데이터 분석 플랫폼인 Oracle Database EE 19c에 패키지로 포함되어 있습니다. ML 어플리케이션 구현을 위한 language로 SQL을 사용하며 ML 알고리즘, 통계 알고리즘, R 페키지 등을 포함합니다. Oracle DataMiner, 아파치 제플린 노트북 뿐만 아니라 다양한 개발 툴에서 사용 가능합니다.

##### 1. Data Miner 워크플로우 시작
- 이 예제를 진행하기 위해서는 Oracle Sqldeveloper 19, Oracle database 19c EE이상의 버전이 필요합니다.
- Oracle Download Center(https://www.oracle.com/downloads/) 에서 Sqldeveloper 19와 Oracle Database 19c 이상의 버전을 다운로드하여 설치를 하십시오. 설치 방법은 오라클 문서를 참조하십시오.
- Data Miner를 처음 실행하면 해당 스키마 유저에 Data Miner와 관련한 repository와 샘플 데이터가 설치됩니다. 이 예제는 샘플데이터(INSUR_CUST_LTV_SAMPLE)를 사용합니다.
- Sqldeveloper를 실행하여 메뉴바에서 "Tools"-"Data Miner"-"Make Visible" 선택합니다.
- 다시 DataMiner 윈도우 창 오른쪽에 있는 메뉴에서 "Connections"-"New project"-"프로젝트명" 을 입력합니다.
- 입력한 프로젝명에서 "New-Workflow"를 선택하여 ML을 위한 워크플로우 창을 생성합니다.

 ![](./DataMiner_Screen_shot/create_wf.jpeg)

##### 2. 소스 데이터 준비

- 앞에서 생성한 워크플로우 창에서 ML 모델 빌드를 시작합니다. 처음으로 해야할 작업은 소스데이터를 준비하는 것입니다. Oracle Macine Learning은 Oracle database에 있는 테이블 또는 뷰를 데이터 소스로 사용합니다. 데이터 준비에 대한 세부적인 방법은 Oracle Data Mining Users's Guide 12c Release 2(https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dmprg/index.html) 를 참고하십시오
- Sqldeveloper 윈도우 창 오른쪽에 있는 "Components" 창 바로 밑에 있는 "Data" 구역에서 "Data Source" 노드를 드래그해서 워크플로우 창에 놓습니다.
- "Define Data Source" 팝업 창에서 모델 빌드에 사용할 테이블 또는 뷰를 선택하고 "Finish" 버튼을 클릭합니다. 예제에서는 "INSUR_CUST_LTV_SAMPLE" 테이블 선택합니다.

 ![](./DataMiner_Screen_shot/drag_datasource1.jpeg)

- 선택한 테이블에 대한 컬럼 정보, 데이터 내용들을 하단에 있는 화면에서 확인할 수 있습니다.
- 데이터 정보를 확인했으면 "Finish" 버튼 클릭해서 데이터 소스 정의를 마칩니다.

 ![](./DataMiner_Screen_shot/drag_datasource2.jpeg)

##### 3. 소스 데이터세트 시각화 및 검사

"Explore Data" 노드와 "Graph" 노드는 준비된 소스 데이터가 제시된 문제를 해결하기 위한 기준을 충족하는지 시각화를 통해 평가 할 수 있는 기능입니다. 모델 빌드 과정에서는 선택적으로 사용 할 수 있습니다.

##### 3.1 Explore 노드 준비

- "Explore Data" 노드를 워크플로우 창에 드래그 드롭 합니다.

 ![](./DataMiner_Screen_shot/exam_data_source_1.jpeg)

- 앞에서 만든 "INSUR_CUST_LTV_SAMPLE" 노드에서 마우스 오른쪽을 클릭하여 팝업 창에서 "Connect" 를 선택 후 방금 만든 "Explore Data" 노드와 연결합니다.

 ![](./DataMiner_Screen_shot/exam_data_source_2.jpeg)

- "Explore Data" 노드를 더블클릭(또는 마우스 오른쪽을 클릭)하여 팝업 창에서 "Edit"를 선택합니다.
- "Edit Explore Data Node" 팝업창에서 "Group By" 리스트 박스에서 "BUY_INSURANCE" 어트리뷰트를 선택합니다.
- "Explore Data" 노드를 선택, 오른쪽 마우스를 클릭하여 팝업창에서 "Run"을 선택합니다.
- 이제 Data Miner는 워크플로우를 저장하고 Explore Data 노드를 처리하는 동안 워크플로우 분할 창의 맨 위에 상태 정보를 표시합니다. 각 노드가 처리될 때 노드 우측상단에 녹색 기어 아이콘이 표시되고, 작업이 완료되면 Data Source 및 Explore Data 노드 테두리에 녹색 체크 표시가 나타납니다.

 ![](./DataMiner_Screen_shot/exam_data_source_3.jpeg)

##### 3.1.1 데이터 세트에 대한 통계분석 정보검토
- Explore Data 노드에서 오른쪽 마우수를 클릭하여 팝업창에서 "View Data"를 선택합니다.
- Explore Data 윈도우 창에서 "Statistics" 탭을 선택하면 아래 그림 처럼 빌드 데이터에 대한 데이터 분포도 등과 같은 통계정보를 그래프 형태로 확인 할 수 있습니다.
- Data Miner는 이전에 정의한 "Group By" Attribute "BUY_INSURANCE" 별로 데이터 세트의 각 Attribute에 대한 다양한 통계를 계산합니다. 출력 열에는 히스토그램 썸네일, 데이터 유형, 고유 값(Distinct values), 고유 백분율(Distinct Percent), 모드, 평균, 중앙값, 최소 및 최대 값, 표준 편차 및 분산이 포함됩니다.
- 데이터 세트에 대한 검토가 끝났으면 "Explore Data" 위도우 창을 닫습니다.

 ![](./DataMiner_Screen_shot/explore_data_3.jpeg)

 ![](./DataMiner_Screen_shot/explore_data_2.jpeg)

##### 3.2 데이터 세트에 대한 시각화
- 오른쪽 위도우의 Components 창에서 "Graph" 노드를 workfolw 창으로 드레그 드롭합니다.
- Data Source(INSUR_CUST_LTV_SAMPLE) 노드와 Graph 노드를 연결합니다.

##### 3.2.1 Histogram 추가
- Graph 노드를 더블 클릭하여 "New Graph" 팝업 창에서 "Histogram" 버튼을 선택하고 밑에 있는 Title에 이름을 입력하고, "Histogram Setting"의 Attribute 리스트 박스에서 AGE 선택, "Group by"의 Attribute 리스트 박스에서 "LTV_BIN"을 선택하고 "OK" 버튼을 클릭합니다.
- Graph 윈도우 창에 Age 관련 히스토그램이 만들어지고 연필 아이콘 오른쪽의 확대 아이콘을 눌러 보다 더 크게 히스토그램을 확인 할 수 있습니다.

 ![](./DataMiner_Screen_shot/graph_data_source1.jpeg)

##### 3.2.2 Box Plot 추가
- Graph 윈도우 창에 두번째 그래프를 만듭니다. Graph 윈도우 창의 오른쪽에 있는 녹색 "+"" 아이콘을 클릭합니다.
- "New Graph" 팝업창에서 오른쪽의 "Box" 버튼을 선택하고, Tile 창에 이름을 입력하고, Box graph Setting 칸의 Attribute 리스트 박스에서 "LTV"를 선택하고 "Group by" Attribute 리스트 박스에서 "MARITIAL_STATUS"를 선택하고 "OK" 버튼을 누르고 완료합니다.  

 ![](./DataMiner_Screen_shot/graph_data_source2.jpeg)

##### 3.2.3 시각화된 그래프를 이용하여 데이터 특성 확인
- Graph 윈도우 창에 두개의 그래프가 만들어진 것을 확인 할 수 있습니다.
- 이와 같이 Data Miner는 데이터 소스의 데이터를 다양한 형태의 그래프로 시각화 할 수 있는 기능을 제공합니다.

 ![](./DataMiner_Screen_shot/graph_data_source3.jpeg)

##### 4. 모델 빌드 및 훈련
- 이제 본격 적으로 고급 수동 분석(High-Level manual analytics)을 시작합니다. Oracle Data Miner의 워크플로우 생성 프로세스는 모델 구축 및 테스트 과정에서 복잡하고 어려운 작업들을 자동화합니다.
- 모델을 만들때 어떤 알고리즘이 업무문제를 가장 잘 해결할 수 있는지 미리 알기가 어렵기 때문에 일반적으로 여러 모델을 생성하고 테스트합니다.
- 완벽한 모델은 없습니다. 최상의 예측모델을 찾는 것은 반드시 가장 높은 정확도(Highest accuracy)로 모델을 결정하는 것이 아니라 업무목표 측면에서 허용할 수 있는 오류유형을 결정하는 문제입니다.

##### 4.1 "Classification" 모델 선택 및 드래그 드롭
- Data Miner 위도우창의 오른쪽 컴포넌트 탭의 "Models" 카테고리에서 "Classification" 아이콘을 드래그해서 워크플로우 창에 놓습니다.
- 느낌표가 있는 Class Build 노드 아이콘이 만들어집니다. 이 노드를 선택하면 윈도우 오른쪽 컴포넌트 창의 하단에 있는 "properties" 창에서 Classification 분석에 쓸수 있는 4가지 유형의 모데 알고리즘이 표시됩니다. Oracle Machine Learning에서는 Classification 모델을 위한 알고리즘으로 GLM, SVM,DT 및 NB를 지원합니다.

 ![](./DataMiner_Screen_shot/build_model_1.jpeg)

##### 4.2 target과 Case ID 지정
- 이제 Data source(INSUR_CUST_LTV_SAMPLE)와 방금 만든 Class Build 노드와 연결합니다.
- "Edit Classification Build Node" 팝업창에서 Target 리스트 박스에서 "BUY_INSURANCE"를 선택하고 Case ID 리스트 박스에서 "CUSTOMER_ID"를 선택하고 "OK" 버튼을 누릅니다.

 ![](./DataMiner_Screen_shot/build_model_2.jpeg)

##### 4.3 모델 평가(Test) 방법 지정
- 이 예제에서는 모델 빌드와 평가를 하나의 프로세스로 묶어서 진행하는 방법을 사용합니다. 물론 빌드와 평가 프로세스를 분리해서 진행 할 수 도 있습니다. 좀더 숙달되면 그 방법을 사용해보십시오.
- 다시 Class Build 노드를 선택합니다. 오른쪽 하단에 Class Build - properties 창에서 빌드할 모델, 빌드할 target, Case ID를 확인합니다.
- 이어진 "Test" 화면에서 모델 구축후에 평가를 어떻게 할 것 인지 관련 정보를 확인 할 수 있습니다. 자동으로 평가 항목이 지정 되지만 수정 할 수도 있습니다.
- 평가에서 사용할 데이터 세트에 대한 범위도 지정 가능합니다. "Split for Test(%)" 박스에 40%로 지정되어 있음을 확인합니다. 이 의미는 모델들이 60%의 데이터로 빌드(트래이닝)되고 나머지 40%로 평가됨을 의미합니다.

 ![](./DataMiner_Screen_shot/build_model_3.jpeg)

- 이 예제를 처음 진행하시는 분의 화면에는 Data Source 노드와 Class Build 노드가 연결 됐음에도 불구하고 Class Build 노드에 warning 표시인 느낌표가 표시되어 있을 수 있습니다.
- 이 warning 정보는 해당 노드를 선택하여 오른쪽 마우스를 클릭하고 팝업창의 "Show event log"에서 확인 할 수 있습니다.

##### 4.4 후보 알고리즘 추가 정보 세팅 및 훈련 실행
- Oracle Data Miner를 이용하여 Oracle Machine Learning 모델을 구축할 때는 모델에 필요한 알고리즘을 자동으로 추천을 받을 수 있습니다. 이 추천된 알고리즘에는 추가적인 세팅이 필요할 수 있습니다.
- 이 예제에서 추천된 Classification 알고리즘 중에 GLM(Generalized Linear Model)에 추가적인 세팅이 필요합니다.

 ![](./DataMiner_Screen_shot/build_model_4.jpeg)

- Class Build 노드에서 오른쪽 마우수를 눌러서 팝업창의 "Advanced Model setting"을 선택합니다.
- "Advanced Model setting" 팝업창의 Model Settings 화면에서 GLM 모델을 선택하고, 화면 중간에 있는 "Algorithm Settings" 탭을 선택합니다.
- 팝업창 화면에 "Ridge Regression" 드롭박스 리스트에서 "Enable"을 선택하고 "OK" 버튼을 누릅니다.
- Class Build 노드에서 오른쪽 마우스를 눌러 "Run"을 선택합니다.
- 이제 Data Miner는 자동으로 모델을 만들고 60%의 데이터 세트로 모델훈련과 40%의 평가용 데이터로 평가까지 끝낸 모델을 만듭니다. 정상적으로 진행이 됐다면 warning이 없이 모든 모델이 만들어집니다.

 ![](./DataMiner_Screen_shot/build_model_5.jpeg)

##### 4.5 후보 알고리즘별 비교
- Class Build 노드에서 오른쪽 마우스를 클릭하고 "View Model" 및 "View Test Results"에서 개별 모델에 대한 세팅 정보, Test 결과를 세부적으로 확인할 수 있습니다.

 ![](./DataMiner_Screen_shot/build_model_6_1.jpeg)

- "Compare Test Result"에서 각 모델을 동시에 동시에 평가를 할 수 있습니다.

 ![](./DataMiner_Screen_shot/build_model_6.jpeg)

##### 4.5.1 후보 알고리즘별 lift(향상도) 확인
- "Compare Test Results" 실행 결과가 Class Build 윈도우 화면에서 "Lift" 탭을 선택라하고 Target Value 리스트 박스에서 "Yes"를 선택하면 아래 화면에 4모델에 대한 Lift 값이 그래프로 표현됩니다.
- 리프트 탭에서 슬라이더 도구를 사용하여 Quanitile 측정점 선을 그래프의 X 축을 따라 이동할 수 있습니다. 슬라이더를 왼쪽이나 오른쪽으로 움직이면 아래쪽의 모델 창에있는 데이터가 자동으로 업데이트됩니다.
- Quantile 범위를 증가 시키면 40 번째 Quantile에 표시된 것처럼 DT 모델의 Lift Cumulative 및 Gain Cumulative %가 다른 모델을 능가합니다. 40 분위를 지나서 올라감에 따라 다른 모델은 Lift 및 Gain의 변화가 크게 나타납니다. 그러나 DT 모델은 계속해서 최상의 Lift 및 Gain 결과를 나타냄을 평가할 수 있습니다.

 ![](./DataMiner_Screen_shot/build_model_7.jpeg)

##### 4.5.2 알고리즘별 Performance Matrix 확인

- Class Build 윈도우 화면의 "Performace Matrix" 탭을 선택하면 각 모델의 Correct Predictions% 를 확인 할 수 있고, 모델을 선택하면 target 값별 세부정보를 확인 할 수 있습니다.
- 이 예제에서 Performance Matrix를 보면 DT 모델이 80 % 이상의 가장 높은 Correct Predictions(맞은 예측도)를 보이고 있고 SVM 및 GLM 모델은 70% 아래로 나타나고 있습니다.
- 아울러 DT 모델 평가 결과의 Details를 보면 보험을 구매하지 않은 고객의 경우 81 % 정확한 예측 결과를 나타내고 보험을 구매하는 고객의 경우 78 % 정확한 예측 결과를 나타냅니다.

 ![](./DataMiner_Screen_shot/build_model_8.jpeg)

##### 4.5.3 모델 평가 결과의 세부 확인
- Lift, Gain 및 Correct Predictions에서 우수하다고 평가된 Decision Tree 모델에 대해 자세히 살펴보겠습니다.
- Class Build 윈도우 창을 닫습니다.
- 다시 Class Build 노드에서오른쪽마우스를 눌러 "View Result"에서 DT 델을 선택합니다.
- DT 모델에 대한 계층구조로 된 그래프 화면이 나타납니다. 트리구조 그래프가 나타나지 않으면 "Tree" 탭을 누르십시오.
- 트리구조에서 "Node2"를 선택합니다.
- 이 예제의 해석은 첫 번째 스플릿은 BANK_FUNDS 속성을 기반으로하고 두 번째 스플릿은 CHECKING_AMOUNT 속성을 기반으로합니다. 노드 2는 BANK_FUNDS가 225.5보다 크고 CHECKING_AMOUNT가 138보다 작거나 같으면 고객이 보험을 구매할 확률이 64.9 %임을 나타냅니다.
- 트리의 맨 아래 레벨에서 CREDIT_BALANCE 속성에 대한 최종 분할이 추가됩니다. 따라서 이 노드는 BANK_FUNDS가 225.5보다 크고 CHECKING_AMOUNT가 138보다 작거나 같고 CREDIT_BALANCE가 1434.5보다 작으면 고객이 보험을 구매할 확률이 69 %임을 나타냅니다.

 ![](./DataMiner_Screen_shot/build_model_10.jpeg)

##### 5. Apply 모델
- 이제 훈련과 평가를 마친 모델을 배포(Apply)를 합니다.

##### 5.1 Apply 모델 선택 및 후보 모델 제거
- 평가 결과가 가장 우수한 DT 모델만 배포하기 위해 나마지 3개의 후보 모델은 제거합니다.
- Class Build 노드를 선택하면 윈도우 우측 아래 Properties 화면에 후보 모델 4개가 나타납니다. 이중에서 DT 모델을 제외하고 나머지 후보 모델은 "X" 표를 눌러(또는 "OutPut" 열의 화살표를 누름) 제거합니다.

![](./DataMiner_Screen_shot/apply_model_1.jpeg)

##### 5.2 Apply 모델 생성 및 Apply 데이터 셋 지정

- Data Miner 윈도우 창 우축의 Components 창의 "Model Operations" 화면에서 "Apply"  아이콘을 드래그해서 워크플오우 창에 내려 놓습니다.
- Oracle Data Miner에서는 Apply 할때 모델 빌드 때와 Apply때 사용하는 테이블이 동일 하더라도 Apply 데이터 소스를 별도로 지정해야 합니다.
- Components 창의 "Data" 창에서 "Data Source"를 드래그해서 워크플로우 창에 내려 놓고 테이블(INSUR_CUST_LTV_SAMPLE)을 찾아서 선택하고 "Finish" 버튼을 누릅니다.
- 이제 방금 만든 데이터 소스를 누르고 위도우 화면 우측에 있는 "Properties" 창에서 Node name(INSUR_CUST_LTV_SAMPLE_Apply)을 지정합니다.
- 역시 Apply Model 노드를 눌러 우측 Properties Details 화면에서 Node name(ApplyModel)을 지정합니다.

![](./DataMiner_Screen_shot/apply_model_2.jpeg)

##### 5.3 Apply노드에 Case ID 지정 및 실행
- 다시 Apply Model 노드를 선택하고 우측 properties 화면에서 "Predictions" 에서 Cale ID 리스트 박스에서 "CUSTOMER_ID"를 찾아 선택합니다.
- ApplyModel 노드의 팝업창에서 "Run"을 누릅니다.

 ![](./DataMiner_Screen_shot/apply_model_3.jpeg)

- 프로세스가 완료되면 모든 워크플로 노드의 경계에 녹색 확인 표시 아이콘이 표시되어 서버 프로세스가 성공적으로 완료되었음을 나타냅니다.

 ![](./DataMiner_Screen_shot/apply_model_4.jpeg)

##### 5.4 모델 실행 결과 저장 테이블 생성
- 마지막으로 모델 실행결과를 저장할 데이블을 만듭니다.
- 윈도우 우측 Components 창에서 "Create Table or View" 아이콘을 워크플로우 창에 드래그 드롭합니다.
- ApplyModel 노드와 OutPut 노드를 연결합니다.
- OutPut 노드를 선택하고 Propoties 창에서 Table Name(RESULT_DT_ABC), Node Name(RESULT_DT_ABC)를 지정합니다.  
- RESULT_DT_ABC 노드에서 "Run"을 누릅니다.
- 백그라운드로 모델 실행 결과가 들어갈 Table이 만들어 지고 실행결과가 저장됩니다.

![](./DataMiner_Screen_shot/result_model_1.jpeg)

##### 5.5 Output 결과 정렬 및 확인
- RESULT_DT_ABC 노드에서 "View Data" 를 선택하여 "RESULT_DT_ABC" 윈도우 창의 "Sort" 텝을 눌러 sort를 할 컬럼들을 선택하고 "OK" 버튼을 누르고 결과를 확인 합니다.

![](./DataMiner_Screen_shot/result_model_2.jpeg)

###### 여기까지

- 지금까지 Oracle Machine Learning 사용 방법을 알아봤습니다. 수고하셨습니다.
------

------
