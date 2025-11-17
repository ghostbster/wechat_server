# -*- coding: utf-8 -*-
import web
from handle import Handle

urls = (
    '/wx', 'Handle',  # 微信服务器会请求这个地址
)

if __name__ == '__main__':
    app = web.application(urls, globals())
    app.run()