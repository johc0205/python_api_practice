# python_api_practice

### 
- callBuildingPermitApi.ipynb는 구글 코랩을 이용하여 코딩했습니다. 따라서 코랩이 아닌 개인 파이썬 환경에서 실행한 경우 구글 드라이브등의 연동이 안되거나 라이브러리가 존재하지 않을 수 있어 설치가 필요할수 있습니다 
 
- 데이터가 너무 많아 서울특별시 영등포구의 데이터 인허가 정보를 가져오게 하였으며 영등포구 내 동들의 코드들을 가져오는 api와 시군구코드, 동코드를 통해 해당 구의 인허가 정보를 가져오는 api를 사용하였고 사용 후 csv파일을 만들어 구글 드라이브 내에 저장하도록 코딩하였습니다.
 
- 공공 데이터 포털의 계정이 필요하며 **국토교통부_건축인허가정보 서비스, 행정안전부_행정표준코드_법정동코드** 두 개의 활용 신청을 하고 고유 API 키를 아래 2번 INSERT_YOUR_KEY에 넣어줘야 합니다.

1. 먼저 코랩에 대부분 설치되어 있었으나 설치가 안된 라이브러리가 없어 설치해줍니다.

```python
pip install xmltodict
```

2. 이후 아래 코드를 실행시켜 csv 파일을 구글 드라이브 내에 저장합니다.

- 구글 드라이브 해당 폴더에 가 저장되었는지 확인합니다.(폴더가 없는 경우 에러가 날 수 있으니 폴더를 생성하거나 경로를 변경하세요)
