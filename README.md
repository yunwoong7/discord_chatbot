<h2 align="center">
Discord Chatbot
</h2>

<div align="center">
  <img src="https://img.shields.io/badge/python-v3.9.16-blue.svg"/>
  <img src="https://img.shields.io/badge/discord-v2.1.0-blue.svg"/>
</div>

Python 서버와 연결이 되면 Bot의 상태가 "온라인"으로 변경하고 사용자와 Bot이 대화하는 방법에 대해 알아보도록 하겠습니다.

------

### # Discord Bot 상태 변경

```client.change_presence()``` Method를 이용하여 Discord Bot의 상태, 활동 및 기타 속성을 설정할 수 있습니다. 

Discord Bot의 상태를 변경하려면 Discord.py 라이브러리에서 메서드를 사용할 수 있습니다(Python을 사용한다고 가정). 이 방법을 사용하면 Bot의 상태, 활동 및 기타 속성을 설정할 수 있습니다. 다음은 Bot의 상태를 "온라인"으로 설정하고 활동을 "대기중"으로 설정하도록 하겠습니다.

<div align="center">
<img src="https://blog.kakaocdn.net/dn/bGy1ax/btrZz52YkDw/faaE4vCn9ZTc8DzewN7LU1/img.png" width="70%">
</div>

현재 Bot의 상태를 확인해보면 "오프라인" 상태입니다. 다음 코드를 작성하여 수행합니다. TOKEN을 확인하는 방법은 이전 글에서 확인하시고 TOKEN을 입력하시면 됩니다. 채널 ID 역시 [채널 ID를 확인하는 방법](https://yunwoong.tistory.com/213) 을 참고하시어 CHANNEL_ID에 붙여 넣으시면 됩니다.

```python
import discord
from datetime import datetime
 
TOKEN = '<TOKEN>'
CHANNEL_ID = '<CHANNEL ID>'
 
 
class MyClient(discord.Client):
    async def on_ready(self):
        print('Logged on as {0}!'.format(self.user))
        await self.change_presence(status=discord.Status.online, activity=discord.Game("대기중"))
 
 
intents = discord.Intents.default()
intents.message_content = True
client = MyClient(intents=intents)
client.run(TOKEN)
```

서버가 수행이 되면 아래와 같이 Bot의 상태와 활동 내용이 변경됩니다.

<div align="center">
<img src="https://blog.kakaocdn.net/dn/72hQX/btrZzIf3FFF/tl3zB0XozaMPMoekZk3ykK/img.png" width="70%">
</div>

Discord Bot이 종료되거나 서버에서 연결이 끊어지면 상태가 자동으로 "오프라인"으로 설정됩니다. 

### # Discord Bot과 대화하기

Bot과 인사하고 요일, 현재 시간에 대해 답이 가능하도록 코드를 추가하도록 하겠습니다.

```python
import discord
from datetime import datetime
 
TOKEN = '<TOKEN>'
CHANNEL_ID = '<CHANNEL_ID>'
 
 
class MyClient(discord.Client):
    async def on_ready(self):
        print('Logged on as {0}!'.format(self.user))
        await self.change_presence(status=discord.Status.online, activity=discord.Game("대기중"))
 
    async def on_message(self, message):
        if message.author == self.user:
            return
 
        if message.content == 'ping':
            await message.channel.send('pong {0.author.mention}'.format(message))
        else:
            answer = self.get_answer(message.content)
            await message.channel.send(answer)
 
    def get_day_of_week(self):
        weekday_list = ['월요일', '화요일', '수요일', '목요일', '금요일', '토요일', '일요일']
 
        weekday = weekday_list[datetime.today().weekday()]
        date = datetime.today().strftime("%Y년 %m월 %d일")
        result = '{}({})'.format(date, weekday)
        return result
 
    def get_time(self):
        return datetime.today().strftime("%H시 %M분 %S초")
 
    def get_answer(self, text):
        trim_text = text.replace(" ", "")
 
        answer_dict = {
            '안녕': '안녕하세요. MyBot입니다.',
            '요일': ':calendar: 오늘은 {}입니다'.format(self.get_day_of_week()),
            '시간': ':clock9: 현재 시간은 {}입니다.'.format(self.get_time()),
        }
 
        if trim_text == '' or None:
            return "알 수 없는 질의입니다. 답변을 드릴 수 없습니다."
        elif trim_text in answer_dict.keys():
            return answer_dict[trim_text]
        else:
            for key in answer_dict.keys():
                if key.find(trim_text) != -1:
                    return "연관 단어 [" + key + "]에 대한 답변입니다.\n" + answer_dict[key]
 
            for key in answer_dict.keys():
                if answer_dict[key].find(text[1:]) != -1:
                    return "질문과 가장 유사한 질문 [" + key + "]에 대한 답변이에요.\n" + answer_dict[key]
 
        return text + "은(는) 없는 질문입니다."
 
 
intents = discord.Intents.default()
intents.message_content = True
client = MyClient(intents=intents)
client.run(TOKEN)
```

------

<div align="center">
<img src="https://blog.kakaocdn.net/dn/bogKV9/btrZzLwZdcN/6cNq2p9yKvkynxjuxklCt0/img.gif" width="70%">
</div>

Discord는 사용자 및 서버 데이터에 대한 액세스, 실시간 메시징 및 이벤트 처리, 서버, 채널 및 사용자를 만들고 관리하는 기능을 포함하여 광범위한 기능을 API로 제공합니다. 상세한 내용은 [Discord API 문서](https://discordpy.readthedocs.io/en/stable/api.html#discord-api-events)를 참조하시기 바랍니다.