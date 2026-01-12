import random, re

class RangeGen:
    def __init__(self):
        self.ranges = {"1-10":(1,10),"1-50":(1,50),
                      "1-100":(1,100),"custom":None}
    
    def parse(self, txt):
        txt = txt.lower().replace('от','').replace('до','')
        if '-' in txt:
            try:
                a,b = map(int, txt.split('-'))
                return (a,b) if a<b else (b,a)
            except: pass
        nums = re.findall(r'\d+', txt)
        if len(nums)>=2:
            a,b = int(nums[0]), int(nums[1])
            return (a,b) if a<b else (b,a)
        return (1,100)
    
    def get(self, inp):
        if inp in self.ranges and self.ranges[inp]:
            a,b = self.ranges[inp]
        else:
            a,b = self.parse(inp)
        return random.randint(a,b)
    
    def show(self):
        return "Диапазоны: 1-10,1-50,1-100,от X до Y"

r = RangeGen()
print(r.show())
print("Число:", r.get("1-100"))
print("Кастом:", r.get("от 15 до 85"))
