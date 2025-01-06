---
date: 2024-12-06
# description: ""
# image: ""
lastmod: 2024-12-06
showTableOfContents: false
# tags: ["",]
title: "Converting CSV for MS365"
type: "post"
---

Create CSV file to for import new users to the MS365. You will need at least First and last name for users. The script will do everything else:

- Creates Username - `first.last@domain.tld`
- Generate passwords with lenght of 12 (you can update script to have different lenght)
- Creates display name
- generate `emails.csv` file suitable for import to MS365

Script vill look for `export.csv` as input in the same folder as the `.py` script is.

```python
"""
Description KR: Microsoft 365 사용자를 가져오기 위해 이름과 성이 포함된 CSV 파일을 CSV 형식으로 변환합니다
Description EN: Convert a CSV file containing first and last names into a CSV format for importing Microsoft 365 users
"""
import pandas as pd
from unidecode import unidecode
import random
import string

domain_name = "@domain.name" # Update this variable with your own domain / 자신의 도메인으로 이 변수를 업데이트하세요.

# 각 사용자에 대해 길이 12로 암호 생성
# generate password with lenght 12 for each user
def generate_password(lenght=12):
    characters = string.ascii_letters.replace('l', '').replace('O', '') + string.digits
    password = ''.join(random.choice(characters) for i in range(lenght))
    return password

df = pd.read_csv('export.csv', delimiter=';')

df = df.iloc[:, :2]
df.columns = ['Last name', 'First name']

# 공백을 제거하고 악센트가 있는 문자를 암송합니다
# remove spaces and transliterate accented characters
first_name = df['First name'].apply(lambda x: unidecode(x).replace(' ', '').lower().strip())
last_name = df['Last name'].apply(lambda x: unidecode(x).lower().strip())

df['Last name'] = df['Last name'].apply(lambda x: x.capitalize().strip())
df['Username'] = first_name + '.' + last_name + domain_name
# df['Password'] = df.apply(lambda x: generate_password(), axis=1)
df['Display name'] = df['First name'] + ' ' + df['Last name']
df['Job title'] = ''
df['Department'] = ''
df['Office number'] = ''
df['Office phone'] = ''
df['Mobile phone'] = ''
df['Fax'] = ''
df['Alternate email address'] = ''
df['Address'] = ''
df['City'] = ''
df['State or province'] = ''
df['ZIP or postal code'] = ''
df['Country or region'] = ''

# print(df.head())

# 새 파일로 내보내기 전에 열 재정렬
# reorder columns before exporting to new file
ordered_columns = ['Username',
                    'First name',
                    'Last name',
                    'Display name',
                    'Job title',
                    'Department',
                    'Office number',
                    'Office phone',
                    'Mobile phone',
                    'Fax',
                    'Alternate email address',
                    'Address',
                    'City',
                    'State or province',
                    'ZIP or postal code',
                    'Country or region']

df.to_csv('emails.csv', columns=ordered_columns, index=False)
```

Upload the `emails.csv` file to the MS365 user import to create users.
