import os
import requests
import pandas as pd
from datetime import datetime

API_KEY = os.getenv("snOlXoqmO+Auj39eC+RSnF27Y7g8dM+g4YxudtLaq5664xOw1XdFW6JMGQE1Fi/3981bQGnKsooawG67Jn0zTg==")
SLACK_URL = os.getenv("https://hooks.slack.com/triggers/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")
BASE_URL = "http://apis.data.go.kr/1230000/ad/BidPublicInfoService/getBidPblancListInfo"

def format_date(dt):
    return dt.strftime("%Y%m%d%H%M")

def fetch_data():
    now = datetime.now()
    params = {
        'serviceKey': API_KEY,
        'numOfRows': '10',
        'pageNo': '1',
        'type': 'json',
        'inqryDiv': '1',
        'inqryBgnDt': format_date(now.replace(hour=0, minute=0)),
        'inqryEndDt': format_date(now.replace(hour=23, minute=59)),
    }
    r = requests.get(BASE_URL, params=params)
    try:
        return pd.json_normalize(r.json()['response']['body']['items'])
    except:
        return pd.DataFrame()

def send_to_slack(text):
    resp = requests.post(SLACK_URL, json={"text": text})
    return resp.status_code == 200

def main():
    df = fetch_data()
    if df.empty:
        print("📭 조회된 공고가 없습니다.")
        return
    for _, row in df.iterrows():
        msg = (
            f"📢 *{row['bidNtceNm']}*\n"
            f"- 공고번호: {row['bidNtceNo']}\n"
            f"- 기관: {row['ntceInsttNm']}\n"
            f"- 공고일: {row['bidNtceDt']}\n"
            f"👉 http://www.g2b.go.kr"
        )
        send_to_slack(msg)
    print(f"✔️ {len(df)}건 Slack 전송 완료")

if __name__ == "__main__":
    main()
