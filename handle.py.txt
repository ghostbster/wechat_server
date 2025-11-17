# -*- coding: utf-8 -*-
import hashlib
import web
import receive
import time
import os

class Handle(object):
    def __init__(self):
        self.app_root = os.path.dirname(__file__)
        self.templates_root = os.path.join(self.app_root, 'templates')
        self.render = web.template.render(self.templates_root)

    def GET(self):
        # 微信服务器验证URL有效性
        try:
            data = web.input()
            if len(data) == 0:
                return "hello, this is handle view"
            signature = data.signature
            timestamp = data.timestamp
            nonce = data.nonce
            echostr = data.echostr
            token = "your_wechat_token"  # 请务必修改成你自己设置的Token

            list = [token, timestamp, nonce]
            list.sort()
            sha1 = hashlib.sha1()
            for item in list:
                sha1.update(item.encode('utf-8'))
            hashcode = sha1.hexdigest()
            print("handle/GET func: hashcode, signature: ", hashcode, signature)
            if hashcode == signature:
                return echostr
            else:
                return "Verification Failed"
        except Exception as e:
            return f"Error: {e}"

    def POST(self):
        # 处理用户发送给公众号的消息
        try:
            webData = web.data()
            recMsg = receive.parse_xml(webData)
            if isinstance(recMsg, receive.Msg) and recMsg.MsgType == 'text':
                toUser = recMsg.FromUserName
                fromUser = recMsg.ToUserName
                # 这里可以编写你的自动回复逻辑
                content = "你好！感谢你的消息：" + recMsg.Content.decode('utf-8')
                reply_msg = self.render.reply_text(toUser, fromUser, int(time.time()), content)
                return reply_msg
            else:
                print("暂且不处理其他消息类型")
                return "success"
        except Exception as e:
            print(f"Error in POST: {e}")
            return "success"