---
title: python으로 엑셀 읽고 쓰기
date: 2022-05-11 08:05:36
category: dev
draft: false
---

와이프 회사에서 엑셀작업을 하는데 단순작업이 많다고해서 얘기를 들어보니 생각보다 금방 해결할 수 있는 문제들 같아서 겸사겸사 파이썬 공부를 시작했다

라이브러리를 찾아보니 파이썬 데이터 처리를 위한 국민 라이브러리인 `판다스(Pandas)`가 있어 이를 사용해 보기로 하였다

> ✔ 이 글에서 사용된 예제파일은 [여기](https://go.microsoft.com/fwlink/?LinkID=521962)서 다운받을 수 있다

# 1. 판다스 설치하기

먼저 판다스를 설치해줘야 한다

```sh
$ pip install pandas
```

> 📌 `pip`는 자바스크립트의 `npm`처럼 패키지를 관리해주는 프로그램으로 파이썬 v3.4 이상을 설치 시 자동으로 같이 설치된다

이를 설치하면 예제를 통해 간단한 데이터 테이블을 출력해볼 수 있다

```py
import pandas as pd

df = pd.DataFrame(
  {
    "Name": [
      "Braund, Mr. Owen Harris",
      "Allen, Mr. William Henry",
      "Bonnell, Miss. Elizabeth",
    ],
    "Age": [22, 35, 58],
    "Sex": ["male", "male", "female"],
  }
)

print(df)

"""
                       Name  Age     Sex
0   Braund, Mr. Owen Harris   22    male
1  Allen, Mr. William Henry   35    male
2  Bonnell, Miss. Elizabeth   58  female
"""
```

# 2. 판다스로 엑셀 열어보기

판다스는 간단하게 csv, xls, xlsx등을 열어볼 수 있다

그 중에 최신 엑셀파일을 열어보기 위해서는 `openpyxl` 엔진을 사용하기 때문에 설치가 필요하다 [참고](https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html?highlight=read_excel)

```sh
$ pip install openpyxl
```

사용하기에 앞서 판다스에 데이터 구조에 대해 알아볼 필요가 있다

판다스는 `Series`, `DataFrame`, `Panel`이 있는데 이중에 가장 많이 쓰이는 것은 `Series`, `DataFrame`이다

`Series`는 1차원 배열의 값에 대응하는 인덱스를 부여할 수 있는 구조이다

처음의 예시에서 나이값만 출력해보자

```py
print(df["Age"])
"""
0    22
1    35
2    58
Name: Age, dtype: int64
"""
```

이러한 구조를 `Series`라고 하며 `Series`가 모여 하나의 `DataFrame`이 된다

즉, `DataFrame`의 각 cloumn들은 `Series`인 것이다

> 📌 _Each column in a `DataFrame` is a `Series`_

이제 구조를 알아봤으니 엑셀 내용을 읽어 출력해보자

```py
ex = pd.read_excel("sample.xlsx", sheet_name="Sheet1")

print(ex) # DataFrame이 양이 많을 때에는 처음과 끝 5개씩 출력해준다
```

파일 경로와 읽고 싶은 엑셀 시트의 이름을 넣으면 되며 처음 혹은 끝의 데이터를 중점적으로 보고싶다면 [.head()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.head.html?highlight=head), [.tail()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.tail.html?highlight=tail)메소드를 사용하면 된다

```py
ex.head(5) # DataFrame의 처음 5개를 보여줌
ex.tail(3) # DataFrame의 끝 3개를 보여줌
```

각 column(열)의 데이터타입을 알고싶다면 아래와 같이 확인해볼 수 있다

```py
print(ex.dtypes)

"""
PassengerId      int64
Survived         int64
Pclass           int64
Name            object
Sex             object
Age            float64
SibSp            int64
Parch            int64
Ticket          object
Fare           float64
Cabin           object
Embarked        object
dtype: object
"""
```

이 중에 strings를 `object`로 표현하는게 조금 특이하다고 할 수 있다

# 3. 판다스로 엑셀 작성하기

판다스로 엑셀을 작성할 때는 prefix를 to로 바꿔주기만 하면 된다

```py
ex.to_excel("test.xlsx", index=False)

# 'test.xlsx' has been created inside the root directory
```

여기서 `index=False`를 주는 이유는 기본값이 True인데 True일 때 A cloumn(첫 열)에 0, 1, 2, ···과 같이 인덱스를 적어주기 때문이다

> ✔ 간단하게 엑셀 파일의 (행, 열)을 알고싶다면 [.shape](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.shape.html?highlight=shape)을 사용하도록 하자

# 4. 판다스로 엑셀의 특정 데이터 선택하기

> 📌 _Each column in a `DataFrame` is a `Series`_

이 말을 기억하고 있다면 어느정도 감이 올 것이다

## 4-1. 판다스로 특정 column 선택하기

```py
print(ex[["Segment", "Country"]])

"""
              Segment                   Country
0          Government                    Canada
1          Government                   Germany
2           Midmarket                    France
3           Midmarket                   Germany
4           Midmarket                    Mexico
..                ...                       ...
695    Small Business                    France
696    Small Business                    Mexico
697        Government                    Mexico
698        Government                    Canada
699  Channel Partners  United States of America

[700 rows x 2 columns]
"""
```

```py
print(type(ex[["Segment", "Country"]]))
# pandas.core.frame.DataFrame
```

`Series`를 모아서 만들었기 때문에 타입은 `DataFrame`이다

## 4-2. 판다스로 특정 row 선택하기

특정 row를 선택한다는 것은 엑셀에서 필터를 사용한다는 의미와 같다

판다스는 간단한 condition으로 이를 확인할 수 있다

```py
print(ex[["Segment"]] == "Government")

"""
     Segment
0       True
1       True
2      False
3      False
4      False
..       ...
695    False
696    False
697     True
698     True
699    False
```

원하는 Segment column의 값이 'Government'인지 여부를 확인하였다<br />
(`==`뿐만 아니라 `>`, `!=`, `<=`등도 사용이 가능하다)

이 불리언 값으로 표현된 `Series`를 사용하여 `DataFrame`을 필터링 할 수 있다

```py
print(ex[ex["Segment"] == "Government"])

"""
        Segment                   Country    Product Discount Band  Units Sold  Manufacturing Price  ...      COGS     Profit       Date  Month Number  Month Name  Year
0    Government                    Canada  Carretera          None      1618.5                    3  ...   16185.0   16185.00 2014-01-01             1     January  2014
1    Government                   Germany  Carretera          None      1321.0                    3  ...   13210.0   13210.00 2014-01-01             1     January  2014
5    Government                   Germany  Carretera          None      1513.0                    3  ...  393380.0  136170.00 2014-12-01            12    December  2014
8    Government                    France    Montana          None      1899.0                    5  ...   18990.0   18990.00 2014-06-01             6        June  2014
13   Government                   Germany    Montana          None      2146.0                    5  ...   10730.0    4292.00 2014-09-01             9   September  2014
..          ...                       ...        ...           ...         ...                  ...  ...       ...        ...        ...           ...         ...
...
688  Government                    Canada        VTT          High       865.5                  250  ...    8655.0    6058.50 2014-07-01             7        July  2014
690  Government  United States of America        VTT          High       267.0                  250  ...    2670.0    1869.00 2013-10-01            10     October  2013
694  Government                    France        VTT          High       293.0                  250  ...    2930.0    2051.00 2014-12-01            12    December  2014
697  Government                    Mexico    Montana          High      1368.0                    5  ...    6840.0    1299.60 2014-02-01             2    February  2014
698  Government                    Canada      Paseo          High       723.0                   10  ...    3615.0     686.85 2014-04-01             4       April  2014

[300 rows x 16 columns]
"""
```

`or`과 `and`도 사용이 가능한데 중요한 것은 `or`은 `|`로 `and`는 `&`로 표현해야만 한다는 것이다

```py
print(ex[(ex["Manufacturing Price"] == 2) | (ex["Manufacturing Price"] == 3)])

"""
        Segment  Country    Product Discount Band  Units Sold  Manufacturing Price  ...      COGS    Profit       Date  Month Number  Month Name  Year
0    Government   Canada  Carretera          None      1618.5                    3  ...   16185.0  16185.00 2014-01-01             1     January  2014
1    Government  Germany  Carretera          None      1321.0                    3  ...   13210.0  13210.00 2014-01-01             1     January  2014
2     Midmarket   France  Carretera          None      2178.0                    3  ...   21780.0  10890.00 2014-06-01             6        June  2014
3     Midmarket  Germany  Carretera          None       888.0                    3  ...    8880.0   4440.00 2014-06-01             6        June  2014
4     Midmarket   Mexico  Carretera          None      2470.0                    3  ...   24700.0  12350.00 2014-06-01             6        June  2014
..          ...      ...        ...           ...         ...                  ...  ...       ...       ...        ...           ...         ...   ...
630   Midmarket   Canada  Carretera          High      2300.0                    3  ...   23000.0   6670.00 2014-12-01            12    December  2014
631  Enterprise   Mexico  Carretera          High      2821.0                    3  ...  338520.0 -35262.50 2013-12-01            12    December  2013
666  Enterprise   France  Carretera          High      1174.0                    3  ...  140880.0 -16142.50 2014-08-01             8      August  2014
667  Enterprise  Germany  Carretera          High      2767.0                    3  ...  332040.0 -38046.25 2014-08-01             8      August  2014
668  Enterprise  Germany  Carretera          High      1085.0                    3  ...  130200.0 -14918.75 2014-10-01            10     October  2014

[93 rows x 16 columns]
"""
```

> ✔ `or`의 경우 [.isin()](https://pandas.pydata.org/docs/reference/api/pandas.Series.isin.html?highlight=isin)으로 대체할 수 있다

해당 값이 `null`이 아닌 것을 확인하기 위해서는 [.notna()](https://pandas.pydata.org/docs/reference/api/pandas.Series.notna.html?highlight=notna)를 사용하면 된다

## 4-3. 판다스로 특정 row & column 선택하기

이제 두 조건을 합쳐서 필터링해보자

이때에는 다음과 같이 [.loc[row, col]](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.loc.html?highlight=loc)을 사용한다

```py
print(ex.loc[ex["Segment"] == "Government", "Country"])

"""
0                        Canada
1                       Germany
5                       Germany
8                        France
13                      Germany
                 ...
688                      Canada
690    United States of America
694                      France
697                      Mexico
698                      Canada
Name: Country, Length: 300, dtype: object
"""
```

# 5. 와이프 업무 자동화 하기

와이프의 업무 중 엑셀로 정리하는 업무가 있는데 개괄적인 업무 흐름을 보면

1. 출고된 택배의 송장번호, 출고된 물품을 엑셀로 정리

2. 해당 엑셀을 바탕으로 재고관리엑셀파일을 정리

사실 나는 이 업무 흐름이 썩 효율적이라고 생각은 하지 않는다

지금은 `1.`에서 출고된 물품을 정리할 때 **의자1,책상1,신발3,책장1**이런식으로 하나의 셀에 다 넣는 방식으로 하고 있는데 처음부터 따로따로 작성을 한다면 그 작성된 파일을 바탕으로 재고관리 파일은 자동으로 관리되도록 할 수 있다

하지만 모든 업무에는 레거시가 존재하고 변화를 원치않는 사람도 존재한다

그리고 이참에 파이썬을 공부해볼 수 있고 이를 통해 와이프에게 코딩의 중요성을 일깨워주는 계기라고 생각하니 내게는 `비효율적 업무`보단 `괜찮은 기회`로 느껴졌다

## 5-1. 의사코드

```
1. 택배 관리 엑셀을 불러온다
2. result라는 dictionary를 만들어서 모든 물품명들을 넣고 기본값으로 0개를 넣는다
3. '오늘 날짜'에 해당되는 셀만 불러와서 물품명들을 split하여 products라는 list에 담는다
4. 정규표현식을 이용해 list내의 각 물품들이 몇개인지를 더해 result에 해당 물품에 더해준다
5. 재고관리엑셀을 읽어서 기존 row의 length를 알아내고 시작지점을 length + 1로 잡는다
6. 완성된 dictionary를 가지고 length + 1 지점부터 값이 1이상인 것을 적는다
7. 1 ~ 6이 종료되면 파일을 저장한다
```

> 📌 회사 업무이므로 구체적인 엑셀의 구조 등은 밝히지 않도록 하겠다

## 5-2. 의사코드를 코드화하기

1. 택배 관리 엑셀을 불러온다

```py
parcel = pd.read_excel('택배바코드.xlsx', '택배', header=8)
```

2. result라는 dictionary를 만들어서 모든 물품명들을 넣고 기본값으로 0개를 넣는다

```py
result {
  '의자': 0,
  '책상': 0,
  ...
}
```

3. '오늘 날짜'에 해당되는 셀만 불러와서 물품명들을 split하여 products라는 list에 담는다

```py
from datetime import date

TODAY = str(date.today())
products = []

target = parcel.loc[parcel['날짜'] == TODAY, ['물품명']]

for i in target.index:
  product_name = target['물품명'][i]
  split_list = product_name.split(',')

  products += split_list
```

4. [정규표현식](https://alpaca92.github.io/dev/정규표현식에-대한-이해)을 이용해 list내의 각 물품들이 몇개인지를 더해 result에 해당 물품에 더해준 후 출력해준다

```py
def create_inventory_df(keys, today):
  dates = []
  names = []
  amounts = []
  
  for key in keys:
    if result[key]['amount'] > 0:
      dates += [today + ' 택배']
      names += [result[key]['name']]
      amounts += [result[key]['amount']]
      
  delivered_today = pd.DataFrame(
    {
      '날짜': dates,
      '제품명': names,
      '출고': amounts
    }
  )
  
  print(delivered_today)
```

## \*references

1. [딥 러닝을 이용한 자연어 처리 입문](https://wikidocs.net/book/2155)

2. [Pandas](https://pandas.pydata.org/)
