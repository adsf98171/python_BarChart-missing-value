# python_BarChart-missing-value
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.animation as animation
from IPython.display import HTML
import numpy as np
plt.rcParams['font.sans-serif'] = ['Microsoft JhengHei']


df = pd.read_csv('https://quality.data.gov.tw/dq_download_csv.php?nid=87672&md5_url=a7cf7ecff5466dd8bb4eb38ae65b9e38')
df.head(100)
df.dtypes # 發現rain fall有遺失值且被當作文字
df['rainfall'].unique()


import numpy as np
for rainfall in df:
    df.loc[df['rainfall']=='XXX' , rainfall] = np.nan # Fab7.1 有提到如何把遺失值找出
df['rainfall'].unique()

df = df.dropna() #去掉遺失值
df['rainfall'].unique() #確認是否已無遺失值
df['rainfall'] = df['rainfall'].astype(float) # 修改rainfall為浮點值
df.dtypes

df[df.observeDate =='2017-08']

df['observatory'].unique()


basic = df[df['observeDate']=='2017-08'].sort_values('rainfall').tail(10)
fig, ax = plt.subplots(figsize=(15, 8))
ax.barh(basic['observatory'], basic['rainfall'])

colors = dict(zip(
   ["義竹工作站", "茶業改良場", "農業試驗所", "蘭陽分場", "台中農改場","桃園農改場","苗栗農改場","台東斑鳩分場","高雄農改場",
    "恆春畜試分所","雲林分場",'畜產試驗所','花蓮農改場','五峰工作站','溪頭營林區'],
   ["#2E86AB", "#424B54", "#00A6A6", "#F24236", "#9E643C", "#f7bb5f", "#EDE6F2","#E9D985", "#8C4843", "#90d595",'#aafbff',
    '#90ee90','#90d595','#adb0ff','#eafb50']
))


fig, ax = plt.subplots(figsize=(16, 9))
def race_barchart(input_year):
   dff = df[df['observeDate'].eq(input_year)].sort_values(by='rainfall', ascending=True).tail(10)
   ax.clear()

   ax.barh(dff['observatory'], dff['rainfall'], color=[colors[x] for x in dff['observatory']],height=0.8)
   dx = dff['rainfall'].max()/200
   
   for i, (value, name) in enumerate(zip(dff['rainfall'], dff['observatory'])):
       ax.text(0, i,name+' ',size=16, weight=600, ha='right', va='center')
       ax.text(value+dx, i,f'{value:,.0f}',  size=16, ha='left',  va='center')
           
   ax.text(0.9, 0.2, input_year[:7].replace('-','/'), transform=ax.transAxes, color='#777777', size=72, ha='right', weight=1000)
   ax.text(0, 1.06, '當月降雨量', transform=ax.transAxes, size=14, color='#777777')
   ax.text(0.59, 0.14, '總降雨量'+str(int(dff['rainfall'].sum())), transform=ax.transAxes, size=24, color='#000000',ha='left')
   ax.tick_params(axis='x', colors='#777777', labelsize=12)
   ax.xaxis.set_ticks_position('top')
   ax.set_yticks([])
   ax.margins(0, 0.01)
   ax.grid(which='major', axis='x', linestyle='-')
   ax.text(0, 1.15, '近2年降雨量',
               transform=ax.transAxes, size=24, weight=600, ha='left', va='top')

   plt.box(False)
   
race_barchart('2017-08')


from matplotlib.animation import FuncAnimation
month = list(set(df.observeDate.values))
month.sort()



fig, ax = plt.subplots(figsize=(16, 9))
animator = animation.FuncAnimation(fig, race_barchart, frames=month)
HTML(animator.to_jshtml())
