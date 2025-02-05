---
title: "MadeWithDefold Jam 2023"
date: 2023-05-21
tags: [gamejam, tiles, defold]
summary: "На прошлой неделе завершился [MadeWithDefold Jam 2023](https://itch.io/jam/madewithdefold-jam-2023). В этой статье я рассказываю про свою игру для джема и как в ней реализован авто-тайлинг."
draft: true
cover:
  image: "/images/landscape-tiler.png"
---

## Landscape Tiler

На прошлой неделе завершился [MadeWithDefold Jam 2023](https://itch.io/jam/madewithdefold-jam-2023). Этот происходит каждый год, благодаря энтузиазму [@paweljarosz](https://paweljarosz.itch.io/).

Когда путем голосования была определена тема "[Бесконечный геймплей](https://forum.defold.com/t/madewithdefold-jam-2023/73054/12)", сначала я подумал что подойдет любой бесконечный раннер. Но ведь в раннере нем можно проиграть, что противоречит бесконечности. Вспомнил про idle-игры, где нельзя проиграть, но там нет бесконечности — все-таки игра идет по заранее построенной прогрессии. Так где же отсутствует прогрессия и нельзя проиграть? Может быть, в медитативно-созидательных билдерах?

Я стал перебирать свой сундучок идей и нашел механику соединений чанков земли, подобно тому как это происходит в настольных играх по типу Carcassonne или Civilization. В голове сразу нарисовала картина бесконечного билдера на манер Townscaper или Dorfromantik. Планы, конечно, были большими. Дорогие, деревни, маленькие человечки ходящие по своим делам, хозяйственные процессы, корабли пересекающие море, смена дня и ночи...

![Скриншот версии для джема на Itch.io](/images/landscape-tiler-screenshot.png)

Конечно, по ходу джема скоп стал быстро худеть. Было принято решение сделать настолько мало, насколько это еще возможно успеть сделать качественно. Так получилась простая механика авто-тайлинга с приятным визуалом и аудио сопровождением.

Прежде всего, если вы не видели это тайловый билдер, то приглашаю [потыкать мышкой](https://astrochili.itch.io/landscape-tiler).

## Графика

Графических стилей, в которых мой навык рисованивания чуть выше плинтуса, всего три — чернильная ручка, пользовательские интерфейсы в Figma и микро пиксель-арт. Рисовать чернильной ручкой долго, пользовательские интерфейсы тут ни к чему, поэтому я открыл [Aseprite](https://www.aseprite.org/) и начал эксперементировать с его бета функционалом [Tilemap Layers](https://www.aseprite.org/docs/tilemap/). Потрясающий инструмент, очень рекомендую попробовать.

Палитру я выбрал готовую, интуитивно понравилась [Fantasy 24](https://lospec.com/palette-list/fantasy-24). Немного магии, немного запечености и 24 цвета, из которых пока были использованы только 16.

![Aseprite и готовый тайл-сет по итогам второго дня](/images/landscape-tiler-aseprite.png)

И так, шаг за шагом, двигая пальцем по тачпаду макбука, на второй день джема получился тайл-сет. Тайлы вошли в джем-версию такими, какими они удались с первого раза, поэтому здесь нет супер-аккуратности. Но, как мне кажется, в какой-то степени они все-таки передают приятную магию пиксель-арта.

Чтобы не тратить время на переходы между биомами, я попробовал нарисовать тайлы таким образом, чтобы можно было накладывать их слоями друг на друга. В качестве основы — вода, поверх воды — земля, на земле трава, на траве лес или горы. Так же, был сделан набросок дорог и рек, чтобы разместить их на земле и траве, но включить в игру я их не успел.

## Модель

Что из себя представляет игровой мир в Landscape Tiles с точки зрения модели? Это сетка координат целых чисел, на которой можно размещать чанки, соединяя их друг с другом без особых правил. Каждый чанк состоит из слоев биомов. Каждый слой представляет из себя сетку 4x4 бинарных значений, отвечающих на вопрос — присутсвтует в данном узле биом или нет.

Игрок размещает первый чанк на карту, после чего в руке генерируется новый чанк. Генерация чанка пока что очень простая. Случайным образом определяются три биома, после чего, таким же случайным образом, в этих биомах размещаются бинарные значения узлов. Единственный ньюанс, что у биомов есть зависимости, например, узел биома леса автоматиески за собой устанавливает узлы в биомах травы и земли.

От каких-либо правил соденинения чанков друг с другом, как это реализовано в настольной игре Carcassonne, я отказался, так как на балансировку уже не хватало времени. Представление чанков в виде узлов, а не тайлов, автоматически дает возможность визуализировать стыковку любых сторон чанков с помощью авто-тайлинга.

## Авто-тайлинг

По авто-тайлинг написано много статей ([пример 1](https://www.codeproject.com/Articles/106884/Implementing-Auto-tiling-Functionality-in-a-Tile-M), [пример 2](https://gamedevelopment.tutsplus.com/tutorials/how-to-use-tile-bitmasking-to-auto-tile-your-level-layouts--cms-25673)), которые объясняют метод использования бит-масок, который позволяет достаточно дешево определять индекс тайла исходя из значений углов.

![Конвертируем значения углов в индексы тайлов](/images/landscape-tiler-autotiling.png)

Алгоритм авто-тайлинга простой. Четыре угла тайла представляются в виде бинарных значений, которые при конвертации в десятичное число формируют индекс тайла от `1` до `16`. А так я использую биомы слоями, то `0` и `1` в моем случае — это отсутствие и присутствие биома.

С двумя биомами эта методика отлично справляется. Как использовать бит-маски, если в тайле более двух биомов, пока я представляю смутно. Возможно, использовать числа большей значности.

![Сетка биомов превращается в набор тайлов](/images/landscape-tiler-flow.png)

Первый тайл в моем тайл-сете отсутствует, так как по логике слоев, первый тайл — это отсутствие биома (`0000`). Но зачем пропадать пустому месту, если его можно занять дополнительной вариацией заполненного тайла (`1111`)? А при обращении у тайлу с индексом `16` случайным образом выбирать между тайлами `1` и `16`? Правда, в итоге я его даже не нарисовал, так что не столь важно.

![Тайлы отсортированы так, чтобы удобно читать их по бит-маске](/images/landscape-tiler-tilesource.png)

Все тайлы собраны в одном файле `landscape.tilesource`. Для удобства поиска тайла по индексу в рамках биома, я объединил каждый биом в анимацию из 16 кадров с отключенным воспроизведением. Defold позволяет установить курсор анимации программно, чем я и пользуюсь для установки тайла в компонент спрайта.


```lua
go.set(sprite_url, 'cursor', (tile_index - 1) / 15)
```

Следуя алгоритму авто-тайлинга, каждая сетка узлов биома 4x4 превращается в сетку тайлов 5х5, где крайние тайлы являются стыковочными с узлами соседних чанков.

В следующем примере кода я убрал все лишнее, что касалось стыковки чанков и логики выбора между тайлами `1` и `16`, чтобы обратить ваше внимание, насколько этот алгоритм в чистом виде действительно прост.

```lua
local table_concat = table.concat
local table_empty = {}

---Create an empty grid with the same values
---@param size number Width and height of the grid
---@param value any A value to set
---@return table 2-dimensional array with the same values
local function init_grid(size, value)
  local grid = {}

  for x = 1, size do
    local col = {}

    for y = 1, size do
      col[y] = value
    end

    grid[x] = col
  end

  return grid
end

---Create a tile grid based on the node grid
---@param node_grid table 2-dimensional array with node values 0 and 1
---@return table 2-dimensional array with tile indexes from 1 to 16
local function autotile_grid(node_grid)

  -- The tile grid is larger than the node grid by one value
  -- For example, 4x4 -> 5x5
  local tile_grid = init_grid(#node_grid + 1, 0)

  for x = 1, #tile_grid do
    for y = 1, #tile_grid do
    
      -- Get the tile corners based on the grid nodes values
      local corners = {
        (node_grid[x - 1] or table_empty)[y - 1] or 0,
        (node_grid[x] or table_empty)[y - 1] or 0,
        (node_grid[x - 1] or table_empty)[y] or 0,
        (node_grid[x] or table_empty)[y] or 0
      }

      -- Convert a bitmask to the tile index from 1 to 16
      tile_grid[x][y] = 1 + tonumber(table_concat(corners), 2)
    end
  end

  return tile_grid
end

```

## Сцена

Каждый тайл на игровой сцене — это `game object` с компонентом `sprite`. Не очень оптимально для наслоения тайлов друг на друга, так как на один чанк получается около 80-100 объектов.

Так что лучше это оптимизировать, если вместо спрайтов использовать компонент `tilemap` для каждого биома. Причем, можно иметь один компонент `tilemap` для каждого биома на всю карту. А для еще большей оптимизации, многослойные тайлы следует заранее запечь в `png` файл и работать в итоге только с двумя компонентами `tilemap` на сцене — игровым полем и чанком в руке.

![Устройство чанка в Defold](/images/landscape-tiler-chunk.png)

Каждый чанк содержит в себе фабрики биомов, с помощью которых создает экземпляры тайлов. Я хотел использовать одну фабрику для всех биомов, в рантайме устанавливать тип биома и тайл. Но столкнулся с тем, что после изменения анимации спрайта, установка курсора анимации перестает работать, если у этой анимации отключено воспроизведение. Надо не забыть завести тикет бага для Defold.

После стыковки чанков, происходит перерисовка и новая генерация авто-тайлинга. Создавая тайлы, скрипт чанка учитывает крайние узлы соседних восьми чанков, чтобы выполнить корректный авто-тайлинг.

При стыковке и расстыковке чанков, сейчас происходит пересоздание всех тайлов, что приводит к лагам при большом количестве чанков на сцене. Это легко исправить, если перерисовывать только затронутые стыковкой чанки.

![Одна из фигур тетрамино](/images/landscape-tiler-particles.png)

## Приятные мелочи

Небольшая пыльца, летающая над чанком — это система частиц, использующая простой белый пиксель. Разная направленность и скорость эммитера при стыковке чанка, размещении и уничтожении. Минималистично, дешево, красиво.

В последний день джема я всерьез думал писать игровую музыку и звуки. Меня ждал Model Samples, напичканный отборными сэмплами, и творческий вечер на балконе.

![Elektron Model Samples в другой день и для другой игры](/images/model-samples.jpg)

Но перед этим, для теста, я подключил в игру готовые звуки из моей прошлой работы, [День Кота](https://astronachos.com/catsday/). И они настолько удачно подошли, что за пару минут полностью закрыли собой вопрос звука для джема.

Дальнейшая запись гифок, скриншоты, оформление публикации джема, написание постов в социальные сети — все это заняло достаточно много времени, чтобы еще и успеть создать с нуля качественный звук. Кто-то скажет использовать ассеты, но нет, звук я всегда предпочитаю делать самостоятельно, так как саунд-дизайн — это одно из моих увлечений.

## Зависимости

Для работы с камерой и конвертации координат экрана в игровой мир я использовал библиотеку [defold-orthographic](https://github.com/britzl/defold-orthographic).

Так же, я обнаружил, что способ генерации случайных чисел, который я использовал ранее

```lua
-- Don't do it on HTML5!
math.randomseed(os.clock() * 10000000)
_ = math.random()
_ = math.random()
_ = math.random()
```

оказался практически бесполезен в HTML5, поэтому я обратился за помощью к библиотеке [defold-random](https://github.com/selimanac/defold-random).

## Редактор Zed

Посреди джема мне на глаза попался новый редактор кода [Zed](https://zed.dev/), в котором оказалось практически все что мне необходимо для разработки на Lua, кроме дебаггера. Он включает из коробки [Lua Language Server](https://github.com/LuaLS/lua-language-server) и все связанные с этим удобства. Ожидаю когда в настройки редактора добавят [поддержку настройки языка для кастомных расширений файлов](https://github.com/zed-industries/community/issues/595), чтобы появилась возможно работать с файлами `*.script` и другими вариациями от Defold, содержащими код Lua.

![Проект Landscape Tiler в редакторе Zed](/images/landscape-tiler-zed.png)

Но в последние дни джема скоростная разработка меня завела в клубок кода, который я уже был не в состоянии отдладить одной лишь командой `print()`, поэтому, для отладки с точками остановы пришлось вернуться в VSCode.

## Время

Джем длился 8 дней. Я обещал считать время на разработку игр, но во время гейм-джема это правило сломалось.

- *8 мая*. Поиск идей, сбор референсов.
- *9 мая*. Графический тайл-сет в Aseprite.
- *10 мая*. *Ничего* (редактор Zed захватил все мое внимание).
- *11 мая*. *Ничего* (понял, что пора резать скоп).
- *12 мая*. Первый чанк и авто-тайлинг, повороты.
- *13 мая*. Стыковка чанков друг с другом.
- *14 мая*. Авто-тайлинг между чанками, очистка карты.
- *15 мая*. Системы частиц, звук, оформление публикации.

 После джема:

- *16 мая*. Оценка игр других участников джема.
- *21 мая*. Написание этого поста.
- *22 мая*. Написание этого поста.

Обычно, я тратил от 4 до 6 часов по вечерам, от заката до глубокой ночи. Если оценить абстрактно, то получается около **30 часов** на джем, **6 часов** на оценки других участников и около **12 часов** на этот пост.

## Итоги джема

**Landscape Tiler** не занял первых мест в общей номинации, что справедливо, ведь это даже не игра. А вот победители сделали [полноценные игры](https://itch.io/jam/madewithdefold-jam-2023/results), которые, как я считаю, справедливо достойны этих мест. Самое поразительное, что первые два места заняли разработчики, которые никогда прежде не делали игры на Defold!

Тем не менее, мне удалось занять **#1** в категории [Presentation](https://itch.io/jam/madewithdefold-jam-2023/results/presentation-audiovisuals-style-impression) 🎉.

![](/images/landscape-tiler-result.png)