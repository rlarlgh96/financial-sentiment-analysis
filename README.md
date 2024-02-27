# 뉴스 감성 분석 및 주가 예측

## 실험 목적
국내에서 보도되는 주식 관련 뉴스들의 감성을 분석하고, 감성 점수가 실제 주식의 가격 또는 움직임에 영향을 주는지 알아본다.

## 실험 가설
* 이 프로젝트에서는 삼성전자를 대상으로 분석을 진행했다.
* 이 프로젝트에서는 전날의 삼성전자 관련 뉴스가 다음날 주가에 반영될 것이라 가정하고 실험을 진행했다.

## 실험 방법

### 1. 감성 점수를 활용한 전날 대비 주가의 방향성 예측
* 전날 감성 점수가 0보다 클 때 상승, 0보다 작을 때 하락, 0일 때 유지로 예측한다.
* 이를 실제 주가와 비교하여 그 정확도를 산출한다.

### 2. 다중 선형 회귀 분석
* 감성 점수와 코스피200 전체 산업군 지수를 독립 변수로 갖는 다중회귀모형을 수립한다.
  1. Y: 삼성전자 주가, X: KOSPI200 전체 산업군 지수, 삼성전자 감성 점수
  2. Y: 코스피 지수, X: KOSPI200 전체 산업군 지수, 삼성전자 감성 점수

* 감성 점수를 나타내는 독립 변수가 제거되기 전까지 Backward Elimination을 진행해 최적의 모형을 도출한다(significance level(유의 수준)은 0.1로 한다).
* 최적의 모형에서 감성 점수를 나타내는 독립 변수의 p-value(유의 확률)를 확인한다.
* 위의 과정을 통해 각 모형이 통계적으로 유의미한지 확인한다.

## 데이터 수집
* 뉴스 데이터는 네이버 증권으로부터 Selenium 패키지를 사용해 삼성전자 관련 뉴스들의 헤드라인을 크롤링하여 수집했다.
* 삼성전자 주가와 코스피 지수 데이터는 야후 파이낸스로부터 pandas-datareader 패키지를 사용해 수집했다.
* 코스피200 전체 산업군 지수 데이터는 한국거래소(KRX)로부터 Pykrx 패키지를 사용해 수집했다.
* 2022년 1월 1일부터 2022년 11월 30일까지의 데이터를 수집했다.

## 데이터 전처리
* 감성 점수 산출에는 VADER를 사용했다.
* VADER는 영문 데이터만을 지원하고 있어, 수집한 뉴스 데이터를 네이버 파파고 번역기를 사용해 영문으로 변역하여 저장했다.
* VADER로 산출한 네 종류의 점수 중 종합 점수를 감성 점수로 사용했다.
* 각 뉴스 데이터의 감성 점수를 일자별로 평균하여 합산했다.
* 앞선 실험의 가정에 따라 데이터 인덱스를 활용해 테이블 간 날짜를 맞추는 작업을 진행했다. 이와 동시에 감성 점수 테이블에서 주식 휴장일에 해당하는 데이터를 제거하는 작업을 진행했다.

## 실험 결과

### 1. 감성 점수를 활용한 전날 대비 주가의 방향성 예측
* 앞선 실험 방법에 따라 주가의 움직임을 상승은 1, 하락은 -1, 유지는 0으로 예측했다.
* 그 결과 42.53%의 정확도를 보였다.

### 2. 다중선형회귀 모형 분석
* 앞선 실험 방법에 따라 도출한 최적의 모형은 다음과 같다.

  1. Y: 삼성전자 주가, X: KOSPI200 전체 산업군 지수, 삼성전자 감성 점수
    <img width="600" alt="1번모형" src="https://github.com/rlarlgh96/financial-sentiment-analysis/assets/121072239/561cd7ff-17d8-4f02-989e-283234f35b6d">
    
  2. Y: 코스피 지수, X: KOSPI200 전체 산업군 지수, 삼성전자 감성 점수
    <img width="600" alt="2번모형" src="https://github.com/rlarlgh96/financial-sentiment-analysis/assets/121072239/2c046d0f-9959-4eaa-93c6-618be633f9c7">
  
## 결과 분석

### 1. 감성 점수를 활용한 전날 대비 주가의 방향성 예측
* 예측 정확도 수치가 50%에도 못 미치면서 유의미한 결과를 얻지 못했다.
* 이와 같은 결과에 대해 원인을 분석해 본 결과, 다음 두 가지 문제를 발견하였다.
1. 감성 점수 산출의 성능: 전처리된 데이터를 살펴본 결과, 실제 기대하는 점수와 반대되는 점수를 가지는 데이터가 일부 존재했다.
2. 중복되는 내용의 뉴스 데이터: 특정 뉴스에 한해서 중복되는 내용의 뉴스 데이터가 존재했다. 하지만 중복되는 데이터임에도 불구하고, 각각의 점수가 일관되지 않았다.

### 2. 다중선형회귀 모형 분석
* 각각의 최적의 모형에서 감성 점수의 p-value가 다소 높게 나왔으므로, 통계적으로 유의미하다고 보기 어렵다.

## 개선 방안
* 별도의 감성 사전을 구축하여 감성 점수를 산출: 실제 감성분석을 진행할 때 다른 연구 및 논문에서 사용되는 방법이며, 이를 통해 분석 목적에 맞는 보다 정확한 감성 점수를 산출 할 수 있다.
* 감성 점수가 주가에 반영 되는 보다 정확한 시점 가정: 이 실험에서는 임의로 감성 점수가 다음날 주가에 반영될 것이라고 가정하였지만, 통계적 추론을 통해 보다 정확한 시점을 가정한다면 보다 정확한 결과를 기대할 수 있을 것이다.
