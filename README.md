# 微信数据小助手

使用前先看 一步一步教你用wechaty+百度云主机打造一个带你穿越星际的微信机器人[https://aistudio.baidu.com/aistudio/projectdetail/2177502](https://aistudio.baidu.com/aistudio/projectdetail/2177502)， 基础配置都一样。

![](https://ai-studio-static-online.cdn.bcebos.com/ce4f59816c0d4c13ba7a6bda82fa65d1ad1d6db7d8d44b9ea686bf9f54c87617)

![](https://ai-studio-static-online.cdn.bcebos.com/e57b571713454990a80486f170f6aa0510f23dbb7d024e288482500d481c9d52)




## 1.简介
你想导出喜欢得表情包嘛？自定义的，购买的，想看怎么办？导不出来看不了？重要文件能不能默认接受？发的语音、视频能不能自动保存？聊天记录自动保存？那么微信数据小助手来了，可以根据发送的文件类型，按原文件名保存，聊天记录也可以保存下来，完美满足各类需求。

##  2.功能
* 保存接收的图片到本地
* 保存接收的表情到本地
* 保存接收的语音到本地
* 保存接收的视频到本地
* 保存接收的文件到本地
* 保存接收的聊天记录到本地

## 3.外挂文件管理系统
通过文件管理系统挂载数据目录，可提供WEB操作界面

## 4.图片、视频展示
###  v0.2版本
<iframe width=1000 height=800 src="//player.bilibili.com/player.html?aid=420236172&bvid=BV1yU4y1j7NS&cid=400892961&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

###  v0.1版本
<iframe width=1000 height=800 src="//player.bilibili.com/player.html?aid=420236172&bvid=BV1x3411i732&cid=400892961&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

## 5.实现代码


```python
import asyncio
import logging
from typing import Optional, Union
import os
from wechaty_puppet import FileBox, ScanStatus  # type: ignore
from wechaty_puppet import MessageType

from wechaty import Wechaty, Contact
from wechaty.user import Message, Room

logging.basicConfig(level=logging.INFO)
log = logging.getLogger(__name__)

import time


class MyBot(Wechaty):
    """
    listen wechaty event with inherited functions, which is more friendly for
    oop developer
    """

    def __init__(self):
        super().__init__()

    async def on_message(self, msg: Message):
        """
        listen for message event
        """
        from_contact = msg.talker()
        text = msg.text()
        room = msg.room()
        if room:
            await room.ready()

        # 保存图片
        if msg.type() == MessageType.MESSAGE_TYPE_IMAGE:
            img = await msg.to_file_box()
            # save the image as local file
            await img.to_file(os.path.join('img', img.name))
        # 保存视频
        elif msg.type() == MessageType.MESSAGE_TYPE_VIDEO:
            video = await msg.to_file_box()
            # save the video as local file
            await video.to_file(os.path.join('video', video.name))

        # 保存语音
        elif msg.type() == MessageType.MESSAGE_TYPE_AUDIO:
            audio = await msg.to_file_box()
            # save the audio file as local file
            await audio.to_file(os.path.join('audio', audio.name))

        # 保存表情
        elif msg.type() == MessageType.MESSAGE_TYPE_EMOTICON:
            emoticon = await msg.to_file_box()
            # save the audio file as local file
            await emoticon.to_file(os.path.join('emoticon', emoticon.name))

        # 保存文件
        elif msg.type() == MessageType.MESSAGE_TYPE_ATTACHMENT:
            attachment = await msg.to_file_box()
            # save the audio file as local file
            await attachment.to_file(os.path.join('file', attachment.name))

        # 保存聊天记录
        elif msg.type() == MessageType.MESSAGE_TYPE_TEXT:
            txt = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()) + ',' + from_contact.name + ',' + text + '\n'
            print(txt)
            with open('chat_log.csv', 'a', encoding='utf-8') as f:
                f.write(txt)
        print('done')

    async def on_login(self, contact: Contact):
        log.info(f'user: {contact} has login')

    async def on_scan(self, status: ScanStatus, qr_code: Optional[str] = None,
                      data: Optional[str] = None):
        contact = self.Contact.load(self.contact_id)
        await contact.ready()
        print(f'user <{contact}> scan status: {status.name} , '
              f'qr_code: {qr_code}')


bot: Optional[MyBot] = None


async def main():
    """doc"""
    # pylint: disable=W0603
    global bot
    bot = MyBot()
    await bot.start()


asyncio.run(main())

```

![](https://ai-studio-static-online.cdn.bcebos.com/308ffc929989491f84311bfa49f477a6d6444da427624c90b587a7fd209ec127)
![](https://ai-studio-static-online.cdn.bcebos.com/15e75bf356b349b38da4bf6bf9410dd6992c22f45bd14b8f93754057ed9e6d7e)

![](https://ai-studio-static-online.cdn.bcebos.com/7bfba73e2b094db1ad318e467e0e3f5108e78c1ff42a4ec18ab73ac419f58fa2)
![](https://ai-studio-static-online.cdn.bcebos.com/deaaeb07460142b3a89d52aeb4d3f8a79b970519a56a497b91b95afc12d8a1e3)


