+++
description = ""
tags = ["",""]
date = "2017-03-12T23:39:36+01:00"
title = "test"
categories = [""]
draft = true

+++
Test, tu peux pas test!

```html
<section id="main">
  <div>
   <h1 id="title">{{ .Title }}</h1>
    {{ range .Data.Pages }}
        {{ .Render "summary"}}
    {{ end }}
  </div>
</section>
```

```kotlin
package com.oni.donjon.generator

import com.badlogic.gdx.math.MathUtils
import com.oni.donjon.map.TileType

/**
 * @author Daniel Chesters (on 09/05/2017).
 */
class CellularAutomataCaveGenerator(mapHeight: Int = 50, mapWidth: Int = 50) : MapGenerator(mapHeight, mapWidth) {

    init {
        this.tileTypes = Array(mapWidth) { arrayOfNulls<TileType>(mapHeight) }
    }

    override fun generate() {
        initiateMap()
        iteration(5)
        placeSpecialTile(TileType.STAIR_UP)
        placeSpecialTile(TileType.STAIR_DOWN)
    }

    private fun iteration(count: Int) {
        for (i in 0..count - 1) {
            iteration()
        }
    }

    private fun iteration() {
        for (x in 0..mapWidth - 1) {
            for (y in 0..mapHeight - 1) {
                val countWall = countWall(x, y)
                if (countWall >= 5) {
                    tileTypes[x][y] = TileType.WALL
                } else {
                    tileTypes[x][y] = TileType.GROUND
                }
            }
        }
    }

    private fun countWall(x: Int, y: Int): Int {
        var countWall = 0
        for (i in -1..1) {
            for (j in -1..1) {
                if (x + i < 0 || y + j < 0 || x + i > mapWidth - 1 || y + j > mapHeight - 1) {
                    countWall++
                } else {
                    countWall += if (tileTypes[x + i][y + j] === TileType.WALL) 1 else 0
                }
            }
        }
        return countWall
    }

    private fun initiateMap() {
        for (x in 0..mapWidth - 1) {
            for (y in 0..mapHeight - 1) {
                if (x == 0 || y == 0 || x == mapWidth - 1 || y == mapHeight - 1) {
                    tileTypes[x][y] = TileType.WALL
                } else {
                    if (MathUtils.randomBoolean(0.4f)) {
                        tileTypes[x][y] = TileType.WALL
                    } else {
                        tileTypes[x][y] = TileType.GROUND
                    }
                }
            }
        }
    }
}
```

{{< figure src="/media/spf13.jpg" title="Steve Francia" >}}

{{< tweet 666616452582129664 >}}

{{< youtube w7Ft2ymGmfc >}}

{{< gist spf13 7896402 >}}

{{< instagram BMokmydjG-M >}}

