import requests
from urllib.parse import unquote
import xmltodict
import csv
from google.colab import drive
import datetime

# 드라이브 마운트
drive.mount('/content/drive')

#법정동 코드 조회 함수
def getBubJungdong(api_key: str):
    url = 'http://apis.data.go.kr/1741000/StanReginCd/getStanReginCdList' #법정동 코드를 가져오는 api

    params = {
        'serviceKey': api_key, #필수 파라미터
        'pageNo': '1', #필수 파라미터
        'numOfRows': '40', #필수 파라미터
        'locatadd_nm': '서울특별시 영등포구'
    }

    try:
        response = requests.get(url, params=params)
        response.raise_for_status()

        if response.text:
            xml_dict = xmltodict.parse(response.text) #가져온 xml 데이터 파이썬의 json 형태인 딕셔너리 형태로 변경

            result_list = []
            for row in xml_dict.get('StanReginCd', {}).get('row', []): #지역 이름, 코드만 추출하여 저장
                data_dict = {
                    'locatadd_nm': row.get('locatadd_nm'),
                    'locatjumin_cd': row.get('locatjumin_cd')
                }
                result_list.append(data_dict) 
        return result_list

    except requests.exceptions.HTTPError as errh:
        print("HTTP 에러:", errh)
    except requests.exceptions.RequestException as err:
        print("에러:", err)

#건축인허가 정보 조회 함수
def getBuildingPermit(sigunCd: str, bubjungdong: str, api_key: str): 
    url = "http://apis.data.go.kr/1613000/ArchPmsService_v2/getApBasisOulnInfo" #건축 인허가 정보 가져오는 API

    params = {
        'ServiceKey': api_key, #필수 파라미터
        'sigunguCd': sigunCd, #필수 파라미터
        'bjdongCd': bubjungdong #필수 파라미터
    }

    try:
        response = requests.get(url, params=params)
        response.raise_for_status()
        return response.text

    except requests.exceptions.HTTPError as errh:
        print("HTTP 에러:", errh)
    except requests.exceptions.RequestException as err:
        print("에러:", err)

# 번역할 영어-한글 매핑 딕셔너리
translation_mapping = {  #추출한 값이 영어로 되어 있어 한글로 변경하기 위한 딕셔너리(https://www.data.go.kr/tcs/dss/selectApiDataDetailView.do?publicDataPk=15044678 참고)
    'guyukCd': '구역코드',
    'guyukCdNm': '구역코드명',
    'jimokCd': '지목코드',
    'jiyukCd': '지역코드',
    'jiguCd': '지구코드',
    'archGbCdNm': '건축구분코드',
    'archGbCd': '건축구분코드명',
    'platArea': '대지면적(㎡)',
    'archArea': '건축면적(㎡)',
    'bcRat': '건폐율(%)',
    'totArea': '연면적(㎡)',
    'vlRatEstmTotArea': '용적률산정연면적(㎡)',
    'vlRat': '용적률(%)',
    'mainBldCnt': '주건축물수',
    'atchBldDongCnt': '부속건축물동수',
    'mainPurpsCd': '주용도코드',
    'mainPurpsCdNm': '주용도코드명',
    'hhldCnt': '세대수(세대)',
    'hoCnt': '호수(호)',
    'fmlyCnt': '가구수(가구)',
    'totPkngCnt': '총주차수',
    'stcnsSchedDay': '착공예정일',
    'stcnsDelayDay': '착공연기일',
    'realStcnsDay': '실제착공일',
    'archPmsDay': '건축허가일',
    'useAprDay': '사용승인일',
    'crtnDay': '생성일자',
    'rnum': '순번',
    'platPlc': '대지위치',
    'sigunguCd': '시군구코드',
    'bjdongCd': '법정동코드',
    'platGbCd': '대지구분코드',
    'bun': '번',
    'ji': '지',
    'mgmPmsrgstPk': '관리허가대장PK',
    'bldNm': '건물명',
    'splotNm': '특수지명',
    'block': '블록',
    'lot': '로트',
    'jimokCdNm': '지목코드명',
    'jiyukCdNm': '지역코드명',
    'jiguCdNm': '지구코드명'
}

# API 키를 unquote 함수로 디코딩
api_key = unquote("INSERT YOUR KEY") #발급받은 API키 중 Decoding 키를 입력하세요
siGunGu = {}
bubJungDong = {}

result_list = []

# 함수 호출 및 결과 저장
seoulCodeList = getBubJungdong(api_key) #법정동 코드 데이터를 호출하여 변수에 저장
for sigungu in seoulCodeList:
    sigungu_impl = sigungu['locatadd_nm'].split()

    if len(sigungu_impl) == 2: #추출 정보가 동이 아닌 구 정보인 경우 시군구 딕셔너리에 해당 구와 그에 따른 코드를 저장
        siGunGu[sigungu_impl[1]] = sigungu['locatjumin_cd'][0:5]
    else: 
        if siGunGu.get(sigungu_impl[1]) is None: #딕셔너리에 해당 구 정보가 없는 경우 저장시켜주는 로직
            siGunGu[sigungu_impl[1]] = sigungu['locatjumin_cd'][0:5]
        bubJungDong[sigungu_impl[2]] = sigungu['locatjumin_cd'][5:] #법정동 코드를 저장

        # 건축인허가 정보 가져오기
        result = getBuildingPermit(siGunGu[sigungu_impl[1]], sigungu['locatjumin_cd'][5:], api_key) #추출한 시군구코드, 법정동 코드를 활용해 건축 인허가 정보 호출
        if result is not None:
            result_dict = xmltodict.parse(result)
            impl_list = []
            for k in (result_dict['response']['body']['items']['item']): #각 변수의 key가 영어로 되어있어 한글로 변경해주는 작업
              translated_dict = {translation_mapping.get(key, key): value for key, value in k.items()}
              impl_list.append(translated_dict)
            result_dict['response']['body']['items']['item'] = impl_list
            result_list.extend(result_dict['response']['body']['items']['item'])


# 현재 날짜를 가져옵니다.
today = datetime.date.today()

# 오늘의 연월일을 문자열로 변환합니다. 예: "20231117"
date_str = today.strftime("%Y%m%d")

# 기존 파일 경로에서 파일 이름을 추출합니다.
file_name = "/content/drive/MyDrive/rpa_test/buildPermit_Yeongdeungpo.csv"
file_path, extension = file_name.rsplit('.', 1)

# 새로운 파일 경로를 생성합니다.
csv_file_path = f"{file_path}_{date_str}.{extension}" #파일 이름 뒤에 추출한 날짜 추가

with open(csv_file_path, mode="w", newline="", encoding="utf-8") as csv_file: #csv 파일로 저장
    writer = csv.writer(csv_file)

    # 헤더 쓰기
    header_written = False

    # 결과값 쓰기
    for result_item in result_list:
        if not header_written:
            # 헤더 쓰기
            writer.writerow(result_item.keys())
            header_written = True

        # 결과값 쓰기
        writer.writerow(result_item.values())

print(f"CSV 파일이 {csv_file_path}에 생성되었습니다.")
