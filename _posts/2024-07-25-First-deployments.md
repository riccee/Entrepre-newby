The next steps were uploading it to a platform that would let me sell the data I collected, this is where Apify.com came in. I setup an account and after changing some minor aspects of the code, I was able to deploy it on the site. To date I have had no paying customers, but I have averaged 6-7 free trial users each month. This demonstrates that there could be an interest for an app like this out there, but it needs refining. Below are some of the code changes I had to make.

Utlizing their actors to get user input:
```python
async with Actor:
  actor_input = await Actor.get_input() or {}
  url = actor_input.get('domain')
  #rest of the code
```

Alternating way to return data:
```python
await Actor.push_data({'overview': overview, 'competitors': competitors})
```
