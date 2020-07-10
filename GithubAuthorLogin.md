# Author Login for github

**1、调用https://github.com/login/oauth/authorize?client_id=GITHUB_CLIENT_ID&scope=user,public_repo **

**2、指定回调地址，1操作之后会调用改地址，返回一个 code **

**3、调用https://github.com/login/oauth/access_token?client_id=GITHUB_CLIENT_ID&client_secret=GITHUB_CLIENT_SECRET&code=code 获取access_token **

**4、调用https://api.github.com/user?access_token=accessToken&scope=public_repo%2Cuser&token_type=bearer 获取用户github信息 **
