# -*- coding: utf-8 -*-
import xml.etree.ElementTree as ET

def parse_xml(web_data):
    if len(web_data) == 0:
        return None
    xmlData = ET.fromstring(web_data)
    msg_type = xmlData.find('MsgType').text
    if msg_type == 'text':
        return TextMsg(xmlData)
    elif msg_type == 'event':
        return EventMsg(xmlData)
    # 可以根据需要添加 image, location 等消息类型
    else:
        return Msg(xmlData)

class Msg(object):
    def __init__(self, xmlData):
        self.ToUserName = xmlData.find('ToUserName').text
        self.FromUserName = xmlData.find('FromUserName').text
        self.CreateTime = xmlData.find('CreateTime').text
        self.MsgType = xmlData.find('MsgType').text
        self.MsgId = xmlData.find('MsgId') is not None and xmlData.find('MsgId').text or ""

class TextMsg(Msg):
    def __init__(self, xmlData):
        super().__init__(xmlData)
        self.Content = xmlData.find('Content').text.encode("utf-8")

class EventMsg(Msg):
    def __init__(self, xmlData):
        super().__init__(xmlData)
        self.Event = xmlData.find('Event').text