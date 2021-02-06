---
author: Robertus Diawan Chris
title: 'Instasaver: Save Your Chosen Instagram Posts'
date: 2020-11-09
description: Save your chosen instagram posts, efficiently.
tags: [No one asked, English]
ShowToc: true
TocOpen: false
---

> A brief explanation about instasaver, a tool to save instagram post build with instaloader python module and streamlit.

## Background Story

> Nothing fancy, you should probably skip this.

To make things short, basically i was inspired by Kevin Hazy's project [here](https://instasave.egoist.sh/). After i see Kevin's project, i was like "can i make almost the same thing with python?" and that was the trigger.

If you ask me why i want a tool (kind of) to save instagram post, that's because i have a hard time to save video memes on instagram and i don't really want to use a screen recorder (said the guy who made something that took longer than learning how to use screen recorder and edit the result 👀). So anyway, that's my motivation to made this. **For the memes!**

## Instaloader Python Module

>In this part, i'm only gonna explain the part that i've used and tried along with the problem that i've encountered. So for the full documentations, you can check [here](https://instaloader.github.io/module/instaloader.html).

### Instaloader Feature That I Used

There're a lot of things you can do with instaloader like saving instagram stories, follower list, following list, and so on, but for this project i only use saving post feature. Why i only use that feature? because other features require login to instagram account, i'll explain on that later.

So, even only saving post feature has a lot going on but i'm not quite sure how to implement that, the only thing i'm sure is how to implement saving post using a url (well, also other features that require login too). If you want to learn other features you can check their documentations which is quite confusing. Well, maybe i'm just a complete idiot but you know what, you're gonna have other complete idiots that didn't understand their documentations. I don't really want to be hard on them but they could have done better, especially on the examples (not only advance examples but basic examples too).

### Instaloader Feature That I've Tried

Apart from saving post feature, i've also implemented saving stories, following list, and follower list. As i've mentioned above, those three features require login to instagram account. Well, you can made a fake account to login to instagram but it's not ideal to deploy it. There's this problem when you login quite often so you need to wait around 15 minute to use it again, i mean if i want everyone else to access it then that problem gonna be a hassle. I've never tried to make a lot of fake account and rotate through all of them to login but i might do it later, who knows. If you want to see all the features that i didn't implement in the public version, you can check my github repo [here](https://github.com/bruhtus/instasaver-local).

## Implementation With Streamlit

>In this part, i'm gonna explain the implementation with streamlit and how to deploy on streamlit sharing.

### Instaloader (Main Class)
For starter, in instaloader main class ([Instaloader](https://instaloader.github.io/module/instaloader.html#instaloader.Instaloader)) the parameters that used in this project was `dirname_pattern`, `download_comments`, `download_geotags`, `download_video_thumbnails`, and `save_metadata`.

`dirname_pattern` was to make the default folder to save the file which in this case i use temporary folder because i don't want to save the images or videos on my github repo but download it to my device (whether smartphone or pc).

`download_comments`, `download_geotags`, `download_video_thumbnails`, and `save_metadata` is set to `False` just to make it simple, i just want to download the memes (whether images or videos) and i don't want anything else. You could turn that on to get all that stuff tho.

### Short code

To extract the short code, i used regular expression from this [source](https://github.com/instaloader/instaloader/issues/816#issuecomment-703758078) and then using instaloader `Post.from_shortcode` and `download_post` to finally download the post (saved in temporary folder first and then generate download button to save to device).

### Public Version

What i mean by public version is the version that i deploy so that everyone can use it. For the public version, i only use saving post with url input. The diagram process is below:

![Diagram process public](diagram-process-final.png)

As you see above, you can download one item or multiple items in one post. Implementation for download instagram post with streamlit is first you download the post from url using instaloader `download_post` and saved it to temporary folder and then define exception handling to detect whether it's an image or a video. The implementation is below:

```python
import instaloader
import streamlit as st
import os
from zipfile import ZipFile

post = instaloader.Post.from_shortcode(instaloader.Instaloader.context, shortcode)
instaloader.Instaloader.download_post(post, target=temp) #temp is temporary folder
file_list = [filename for filename in os.listdir]

if len(file_list) == 1: #if only one item in one post
    try:
        st.image(f'{temp}/{file_list[0]}', use_column_width=True) #use_column_width is to resize the width of the image displayed
        st.markdown(download_button(f'{temp}/{file_list[0]}', temp), unsafe_allow_html=True) #download_button is to generate link to download the file
    except:
        st.video(f'{temp}/{file_list[0]}')
        st.markdown(download_button(f'{temp}/{file_list[0]}', temp), unsafe_allow_html=True) #download_button is to generate link to download the file
        
else: #if more than one item in one post
    with ZipFile(f'{temp}/{shortcode}_posts.zip', 'w') as zip: #put all the posts into zip file
        for filename in file_list:
            try:
                st.image(f'{temp}/{filename}', use_column_width=True)
                st.markdown(download_button(f'{temp}/{filename}', temp), unsafe_allow_html=True)
                zip.write(f'{temp}/{filename}')
            except:
                st.video(f'{temp}/{filename}')
                st.markdown(download_button(f'{temp}/{filename}', temp), unsafe_allow_html=True)
                zip.write(f'{temp}/{filename}')

    st.markdown(download_button(f'{temp}/{filename}', temp), unsafe_allow_html=True)
```

please keep in mind that the implementation above is only a partial from all the code, it's just a glimpse of what's going on inside the process. For the full code you can check [here](https://github.com/bruhtus/instasaver).

### Local Version

What i mean by local version is the version that has a more features than the public version that requires login to instagram account. For the local version, i use download the post from url feature (like the public version), download stories (download all the stories of the user, public profile only or you already followed the private account), and download following and follower list.

#### Download stories

For download stories, i used `download_stories` module with user id as input and zip all the stories. Below is a diagram process of the download stories:

![Download stories](diagram-process-download-stories.png)

glimpse of the process:

```python
import instaloader
from zipfile import ZipFile

load = instaloader.Instaloader()
profile = instaloader.Profile.from_username(load.context, username)
profile_id = instaloader.Instaloader.check_profile_id(username)
load.download_stories(userids=[profile_id.userid])

with ZipFile(f'{temp}/{profile.username}_stories.zip', 'w'_) as zip:
    for filename in file_list:
        zip.write(f'{temp}/{filename}')

st.markdown(download_button(f'{temp}/{profile.username}_stories.zip', temp), unsafe_allow_html=True)
```

#### Download following and follower list

For download following and follower list, i used `get_followees` and `get_followers` module with username as input and write all user's following and follower username in `.txt` file.

glimpse of the process (download following list):

```python
import instaloader

load = instaloader.Instaloader()
profile = instaloader.Profile.from_username(load.context, username)

with open(f'{temp}/{profile.username}_following.txt', 'w') as f:
    for following in profile.get_followees():
        f.write(f'{following.username}\n')

st.markdown(download_button(f'{temp}/{profile.username}_follower.txt', temp), unsafe_allow_html=True)
```

### Deployment

I use streamlit sharing and heroku to deploy this project, why i use two services to deploy this project? that's because i want to try web app deployement on heroku and streamlit sharing (which is quite new).

#### Streamlit Sharing
To deploy on streamlit sharing you need to [request an invite](https://www.streamlit.io/sharing) in their website and then after that you can deploy your streamlit app. It takes a few minutes to deploy the first time but after that it's deploy in an instant. You can check the live demo on streamlit sharing [here](https://share.streamlit.io/bruhtus/instasaver/instasaver.py).

#### Heroku
To deploy on heroku, you need a few thing and here's the list:
- `runtime.txt` -> to specify python version.
- `Procfile` -> to specify type of our application and run command, check [here](https://devcenter.heroku.com/articles/procfile) for more info.
- `create_config.sh` (or setup.sh, whatever you want) -> to make config.toml for streamlit to run.

I don't really need to explain about runtime.txt, do i? you just need to type your python version, for example `python-3.7.5`, that's all (i explain it anyway, dammit).

For `.sh` file (in this case i'm gonna name it `create_config.sh` because i'm not creative, sorry), type this:
```bash
mkdir -p ~/.streamlit

echo "[server]
headless = true
port = $PORT
enableCORS = false
" > ~/.streamlit/config.toml
```

For `Procfile`, you don't need to add an extention there. Just `Procfile` is enough. In the `Procfile`, type this:
```
web: sh create_config.sh && streamlit run instasaver.py
```
You don't need to name it `create_config.sh`, be creative, don't be like me.

After all that, you can deploy it using git or from github or something else (i don't remember all the choices). If you want to deploy it using git workflow, you can check [here](https://devcenter.heroku.com/articles/git#:~:text=To%20deploy%20your%20app%20to,heroku%20master%20Initializing%20repository%2C%20done.).

For live demo on heroku, you can check [here](https://instasaver.js.org/).

## References

- [The one who trigger me to made this](https://instasave.egoist.sh/).
- [Instaloader documentations](https://instaloader.github.io/as-module.html).
- [Streamlit](https://www.streamlit.io/).
- [Download instagram stories](https://github.com/instaloader/instaloader/issues/561).
- [View public profile anonymous (more advance version)](https://insta-stories.ru/).
- [Streamlit multiselect nested in if](https://github.com/streamlit/streamlit/issues/826).
- [Streamlit download file](https://discuss.streamlit.io/t/how-to-download-file-in-streamlit/1806/23).
- [Deploy streamlit app on heroku example](https://github.com/tconkling/streamlit_heroku_example).
