---
layout: post
title: "Setup Google Custom Search & Search Images Using Python"
date: 2018-05-16 14:19 +0800
categories: tech
---

# Create an API key
[![](/assets/images/2018-05-18/console-developer-google-create-api-key.png)](/assets/images/2018-05-18/console-developer-google-create-api-key.png)

# Create a custom search engine
[![](/assets/images/2018-05-18/create-custom-search-engine.png)](/assets/images/2018-05-18/create-custom-search-engine.png)

# Setup the created custom search engine
[![](/assets/images/2018-05-18/setup-custom-search-engine.png)](/assets/images/2018-05-18/setup-custom-search-engine.png)

If we want to search images, we should turn on the image search button. The default search option in sites to search section is `search only included sites`, here I choose `search on entire web but emphasize included sites` option. Everything set up, we can try it out by search on the right of the page. As you can see, we use the keyword life, the results are related.

# Use Google Custom Search API with Python
## Install google-api-python-client
```
 https://github.com/google/google-api-python-client
 pip install --upgrade google-api-python-client
```
## Write code to test
```python

from apiclient.discovery import build

service = build("customsearch", "v1",
               developerKey="API_KEY")

res = service.cse().list(
    q='face',
    cx='YOUR_CX',
    searchType='image',
    num=10,
    imgType='clipart',
    fileType='png',
    safe= 'off'
).execute()

if not 'items' in res:
    print 'No result !!\nres is: {}'.format(res)
else:
    for item in res['items']:
        print('=================================================')
        print(item['title'])
        print(item['link'])
```

Now we can search images programmatically, output as following:
```
$ python gcsimgpull.py
=================================================
Best Face Oil for Every Skin Type and How to Use Them | Glamour
https://media.glamour.com/photos/5a425fd3b6bcee68da9f86f8/master/w_644,c_limit/best-face-oil.png
=================================================
Scientists Generated The Perfect Face...And It Looks Like Kim Tae ...
https://www.koreaboo.com/wp-content/uploads/2017/05/Perfect-Face-1.png
=================================================
Face Procedures | Dr Hodgkinson
https://www.drhodgkinson.com.au/wp-content/uploads/2015/12/face-head-2-388x270.png
=================================================
What makes a pretty face? | Science News for Students
https://www.sciencenewsforstudents.org/sites/default/files/scald-image/350_.inline2_beauty_w.png
=================================================
The FACE android | FaceTeam.it
http://www.faceteam.it/wp-content/uploads/2012/07/FACE-Profile.png
=================================================
4 firming creams and masks for a V-shaped face | Her World
http://www.herworld.com/sites/default/files/styles/rec_medium/public/2016/04/4_steps_to_slimming_a_puffy_tired_face_in_10_minutes_t.png?itok=MwfqsgxG
=================================================
Fair & Lovely | Men's MAX Fairness Face Wash
https://www.fairandlovely.in/wp-content/uploads/sites/10/2016/06/mens1_min-1.png
=================================================
Download Woman Face Png Image HQ PNG Image | FreePNGImg
http://www.freepngimg.com/download/face/9-woman-face-png-image.png
=================================================
Mother's Day Gift Voucher | About Face
https://www.aboutface.co.nz/uploads/made/3ebc7775416c2986/Skye_Miss_FQ_after_800_800.png
=================================================
Face Pictures
https://www.withfriendship.com/images/g/31127/scary-face.png
```
## Also we can search in browser address bar
https://www.googleapis.com/customsearch/v1?key=API_KEY&cx=YOUR_CX&q=life&searchType=image

The response data:
```
{
 "kind": "customsearch#search",
 "url": {
  "type": "application/json",
  "template": "https://www.googleapis.com/customsearch/v1?q={searchTerms}&num={count?}&start={startIndex?}&lr={language?}&safe={safe?}&cx={cx?}&sort={sort?}&filter={filter?}&gl={gl?}&cr={cr?}&googlehost={googleHost?}&c2coff={disableCnTwTranslation?}&hq={hq?}&hl={hl?}&siteSearch={siteSearch?}&siteSearchFilter={siteSearchFilter?}&exactTerms={exactTerms?}&excludeTerms={excludeTerms?}&linkSite={linkSite?}&orTerms={orTerms?}&relatedSite={relatedSite?}&dateRestrict={dateRestrict?}&lowRange={lowRange?}&highRange={highRange?}&searchType={searchType}&fileType={fileType?}&rights={rights?}&imgSize={imgSize?}&imgType={imgType?}&imgColorType={imgColorType?}&imgDominantColor={imgDominantColor?}&alt=json"
 },
 "queries": {
  "request": [
   {
    "title": "Google Custom Search - life",
    "totalResults": "6000000000",
    "searchTerms": "life",
    "count": 10,
    "startIndex": 1,
    "inputEncoding": "utf8",
    "outputEncoding": "utf8",
    "safe": "off",
    "cx": "YOUR_CX",
    "searchType": "image"
   }
  ],
  "nextPage": [
   {
    "title": "Google Custom Search - life",
    "totalResults": "6000000000",
    "searchTerms": "life",
    "count": 10,
    "startIndex": 11,
    "inputEncoding": "utf8",
    "outputEncoding": "utf8",
    "safe": "off",
    "cx": "YOUR_CX",
    "searchType": "image"
   }
  ]
 },
 "context": {
  "title": "Ray's Google Custom Search"
 },
 "searchInformation": {
  "searchTime": 0.714577,
  "formattedSearchTime": "0.71",
  "totalResults": "6000000000",
  "formattedTotalResults": "6,000,000,000"
 },
 "items": [
  {
   "kind": "customsearch#result",
   "title": "Life Happens - Interpersonal Wellness Services",
   "htmlTitle": "\u003cb\u003eLife\u003c/b\u003e Happens - Interpersonal Wellness Services",
   "link": "http://interpersonalwellness.com/wp-content/uploads/2016/02/life.jpeg",
   "displayLink": "interpersonalwellness.com",
   "snippet": "Life Happens - Interpersonal Wellness Services",
   "htmlSnippet": "\u003cb\u003eLife\u003c/b\u003e Happens - Interpersonal Wellness Services",
   "mime": "image/jpeg",
   "image": {
    "contextLink": "https://interpersonalwellness.com/life-happens/",
    "height": 462,
    "width": 822,
    "byteSize": 169931,
    "thumbnailLink": "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQkn3d1dcA5mQAPY9Y3SA684qpEJLn2vmFl03Miu2fTKej6qyxCcg25fsw",
    "thumbnailHeight": 81,
    "thumbnailWidth": 144
   }
  },
  {
   "kind": "customsearch#result",
   "title": "Life - how do you see it? What is it to you?",
   "htmlTitle": "\u003cb\u003eLife\u003c/b\u003e - how do you see it? What is it to you?",
   "link": "https://www.one-mind-one-energy.com/images/what-is-life.jpg",
   "displayLink": "www.one-mind-one-energy.com",
   "snippet": "Life - how do you see it? What is it to you?",
   "htmlSnippet": "\u003cb\u003eLife\u003c/b\u003e - how do you see it? What is it to you?",
   "mime": "image/jpeg",
   "image": {
    "contextLink": "https://www.one-mind-one-energy.com/life.html",
    "height": 730,
    "width": 1280,
    "byteSize": 91600,
    "thumbnailLink": "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSWiCLfKlXXbDsf-Zf1BgzGa6PHgDaLP3nZb7iKAFkhg2AzhbWxKNYQBqg",
    "thumbnailHeight": 86,
    "thumbnailWidth": 150
   }
  }
 ]
}
```

# A comprehensive example
Here I wrote a python program to fetch 100 images, and use OpenCV to process the images.

```python

from array import *
import urllib
import cv2
import numpy as np
import os
from apiclient.discovery import build

neg_dir = 'neg'
urls = []

def search(keyword='face', pageSize=10):
    service = build('customsearch', 'v1',
                   developerKey='API_KEY')

    for i in range(10):
        res = service.cse().list(
            q=keyword,
            cx='YOR_SEARCH_ENGINE_CX',
            searchType='image',
            num=pageSize,
            imgType='clipart',
            fileType='png',
            safe='off',
            start=pageSize*i+1
        ).execute()

        if not 'items' in res:
            print 'No result !!\nres is: {}'.format(res)
        else:
            for item in res['items']:
                # print('=================================================')
                # print(item['title'])
                # print(item['link'])
                urls.append(item['link'])


def download(urls):
    if not os.path.exists(neg_dir):
        os.makedirs(neg_dir)

    pic_num = 1

    for i in urls:
        try:
            print('Downloading => pic {}, {}\n'.format(pic_num, i))
            urllib.urlretrieve(i, neg_dir + '/' + str(pic_num) + '.png')
            img = cv2.imread(neg_dir + '/' + str(pic_num) + '.png', cv2.IMREAD_GRAYSCALE)
            # should be larger than samples / pos pic (so we can place our image on it)
            resized_image = cv2.resize(img, (100, 100))
            cv2.imwrite(neg_dir + '/' + str(pic_num) + '.png', resized_image)
            pic_num = pic_num + 1
        except Exception as e:
            print(str(e))

search('trees', 10)
download(urls)
```
It looks like this when it's running:
[![](/assets/images/2018-05-18/pull-images-via-gcs-by-python-and-process-using-opencv3.png)](/assets/images/2018-05-18/pull-images-via-gcs-by-python-and-process-using-opencv3.png)

Maybe it can be failed when downloading some images, but the result is still very good. View the images in file manager:
[![](/assets/images/2018-05-18/gcs-downloading-images-and-processing-via-opencv.png)](/assets/images/2018-05-18/gcs-downloading-images-and-processing-via-opencv.png)

# Reference
[https://stackoverflow.com/questions/22866579/download-images-with-google-custom-search-api]()
[https://developers.google.com/custom-search/json-api/v1/using_rest](https://developers.google.com/custom-search/json-api/v1/using_rest)
[https://developers.google.com/custom-search/json-api/v1/reference/cse/list](https://developers.google.com/custom-search/json-api/v1/reference/cse/list)