# 공인회계시험 채점 간소화 프로그램 개발

#### 배경

업무의 과중화로 최대한 자동화 시키기 위하여 개발

`반년정도 업무를 담당하다 보니 일정 부분 범용 패턴이 보이기 시작했고,  그룹화를 잘 하면 가능하다고 판단되어 시작`

#### 기존의 채점방식

* 회계시험의 경우 일반적인 객관식이 아니라, DB에 입력되는 시험이기 때문에, \
  각 메뉴별로 사용하는 테이블과 UI에 입력된 위치와 컬럼 스키마정보를 정확하게 알고, 테이블명세서를 보면서 하나하나 찾아서 개발해야해서(총 대상 테이블 1000개이상) 오래 걸릴 수 밖에 없음.

#### 개발목표

* 1차 - 테이블과 쿼리를 몰라도 채점이 가능한 로직을 만들기\
  (UI 보이는 메뉴명과, 컴포넌트 명칭만 갖고 채점)
* 2차 - 코딩 없는 프로그램 개발

<figure><img src="https://1.bp.blogspot.com/-ww6uvlWcyr4/YKVerhj18aI/AAAAAAAAHXs/dO3C1bX0YmYJdStry6_LnSruG6oPcmnvACLcBGAsYHQ/s16000/image.png" alt=""><figcaption><p>1차 개선(기존 좌, 개선 우)</p></figcaption></figure>

1차 개선 유형이 50개쯤 넘어갔을때, 이걸 개발자가 할께 아니라.  일반 사용자가 가능하도록 프로그램을 만들 수도 있을것 같다고 생각되어 2차 개선 시작

<figure><img src="https://1.bp.blogspot.com/-5aWwF1XAERA/YKVerlI__eI/AAAAAAAAHXo/EazqFT5P1ds6O1J7sNhc-aRyBqKHVkwQwCLcBGAsYHQ/s16000/image%2B%281%29.png" alt=""><figcaption><p>2차 개선 버전 샘플</p></figcaption></figure>

