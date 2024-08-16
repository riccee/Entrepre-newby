With the architecture in place, the next step was to develop the core functionality: the web scraper. This scraper is designed to extract data from various online sources, specifically focusing on competitor analysis. The scraper works by targeting specific websites, gathering relevant information, and storing it in a structured format. Below are some code snippets that illustrate how the scraper operates, along with explanations of key functions and logic.

How the scraper initializes the connection to the website and get the HTML:
```python
browser = await playwright.chromium.launch(headless=True)
context = await browser.new_context(user_agent='Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36')
page = await context.new_page()
try:
  await page.goto("https://www.similarweb.com/website/"+url+"/competitors/")
  soup = BeautifulSoup(await page.content())
```

How I extract the data for the target company:
```python
domain = soup.find('p', {'class': 'wa-overview__title'}).get_text(strip=True) if soup.find('p', {'class': 'wa-overview__title'}) else ''
```

And for the competitors:
```python
domains = [d.get_text(strip=True) for d in soup.find_all('a', {"class": 'wa-competitors-card__website-title'})]
```

How I use aiohttp to retireve the slug for certain websites:
```python
async def fetch(session, url, payload, headers):
    async with session.post(url, json=payload, headers=headers) as response:
        return await response.json()

async with aiohttp.ClientSession() as session:
        response = await fetch(session, url, payload, headers)
        try:
            slug = response['data']['searchCompanies'][0]['slug']
        except (KeyError, IndexError):
            return []
```
How the data is organized and returned:
```python
for domain, description, totalVisit, categoryId, categoryRank, similarity, employee in zip(domains, descriptions, totalVisits, categoryIds, categoryRanks, similarities, totalEmployees):
    competitors.append({
        "domain": domain,
        "totalVisits": totalVisit,
        "categoryId": categoryId,
        "categoryRank": categoryRank,
        "description": description,
        "similarity": similarity,
        "employees": employee
    })

response_data= {'overview': domain_info, 'competitors': competitors}

return json.dumps(response_data, indent=4)
```


