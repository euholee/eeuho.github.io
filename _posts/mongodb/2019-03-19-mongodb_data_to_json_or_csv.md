---
layout:	post
title: "Mongodb 데이터 json, csv로 추출하기"
data: 2019-03-19 17:55
categories: mongodb
comments: true
---

## JSON 추출

``` mongoexport -d db_name -c collection_name -f "field1,field2,field3" --out ./path/json_filename.json --query="{attribute: value}" ```

모든 필드를 추출하는 경우, 'field1, field2, field3' 대신 '*'을 입력하면 된다.

-c, --query 옵션은 선택 사항. --query 옵션을 이용해서 특정 데이터만 추출할 수 있다.

환경 차이인지는 모르겠으나​ 데비안에서 테스트했을 때 필드 입력시 공백 없이 큰 따옴표로 입력해야 작동했다. ex) "field1,field2,field3"

***

## CSV 추출

``` mongoexport -d db_name -c collection_name -f "field1,field2,field3" --csv --out ./path/csv_filename.csv --query="{attribute: value}" ```

csv 를 추출하는 경우 -f 옵션은 필수사항이다.

-c, --query 옵션은 역시 선택 사항.

환경 차이인지는 모르겠으나​ 데비안에서 테스트했을 때 필드 입력시 공백 없이 큰 따옴표로 입력해야 작동했다. ex) "field1,field2,field3"



반대로 추출된 JSON파일로 다시 임포트 할 수도 있다.


***


## JSON Import

``` mongoimport --db db_name --collection collection_name ./path/json_filename.json ```

CSV파일로도 임포트 할 수 있는지는 테스트해보지 않았지만 아마 가능할 것 같다.
