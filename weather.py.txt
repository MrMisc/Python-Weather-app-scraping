
@client.command()
async def weather(ctx):
    await ctx.send('Forecast or Current?')
    Options = ['Forecast','Current']

    def check(m):
        if m.content in Options:
            return m.content == m.content and m.channel == ctx.channel
    Choice = await client.wait_for('message',check = check, timeout = 20.0)

    if "Forecast" in Choice.content:

        api_address = 'https://api.openweathermap.org/data/2.5/forecast?appid=33fdb643215fe4ead7732a7a8395bbb2&q='
        await ctx.send('Name your city ma boi')

        CityChosen = await client.wait_for('message',check = lambda message: message.author == ctx.author, timeout = 20.0)
        city = CityChosen.content

        await ctx.send('You get to forecast 5 days in advance. Which of these days do you choose - **1st**, **2nd**, **3rd**, **4th** or the **5th** day?')

        Moreoptions = ['1st', '2nd', '3rd', '4th', '5th']
        def checkie(m):
            if m.content in Moreoptions:
                return m.content == m.content and m.channel == ctx.channel

        Genieday = await client.wait_for('message', check = checkie, timeout = 20.0)
        day_rel = int(Genieday.content[0])
        N = 8*day_rel
        n = 8*(day_rel - 1)

        set = list(range(40))

        url = api_address + city
        json_data = requests.get(url).json()
        # Tmin3 = json_data['list'][0]['dt_txt']

        Tmin = []
        Tmax = []
        Humidity = []
        WeatherMain = []
        WeatherDescription = []
        Windspeed = []
        dttxt = []
        for i in range(n,N):
            Tmin.append(json_data['list'][i]['main']['temp_min'])
            Tmax.append(json_data['list'][i]['main']['temp_max'])
            Humidity.append(json_data['list'][i]['main']['humidity'])
            WeatherMain.append(json_data['list'][i]['weather'][0]['main'])
            WeatherDescription.append(json_data['list'][i]['weather'][0]['description'])
            Windspeed.append(json_data['list'][i]['wind']['speed'])
            dttxt.append(json_data['list'][i]['dt_txt'])

        colours = [0xE5FCC2, 0x9DE0AD   , 0x45ADA8, 0x68829E   , 0x547980   , 0x594F4F , 0x453f3f, 0x2A3132 ]
        for i in range(8):

            emb = discord.Embed(title = f"Weather in {city} at {dttxt[i]}", color = colours[i])
            emb.add_field(name = "Atmosphere", value = f"{WeatherMain[i]} / {WeatherDescription[i]} " )
            emb.add_field(name = "Minimum Temperature", value = f"{Tmin[i] - 273.15:.2f} celsius")
            emb.add_field(name = "Maximum Temperature", value = f"{Tmax[i] - 273.15:.2f} celsius")
            emb.add_field(name = "Humidity", value = f"{Humidity[i]}")
            emb.add_field(name = "Wind Speed", value = f"{Windspeed[i]} ms-1")
            emb.set_author(name = 'Projected Weather', icon_url = "https://cdn3.iconfinder.com/data/icons/bebreezee-weather-symbols/690/icon-weather-sunrainheavy-512.png?size=512")
            await ctx.send(content = None, embed = emb)


