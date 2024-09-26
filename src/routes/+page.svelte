<script lang="ts">
    import { cableLines } from "$lib"
    const rooms = cableLines.reduce((acc: string[], item) => {
        if (!acc.includes(item.room)) {
            acc.push(item.room)
        }
        return acc
    }, [])
    rooms.sort()

    const cables = cableLines.reduce((acc: string[], item) => {
        if (!acc.includes(item.cable)) {
            acc.push(item.cable)
        }
        return acc
    }, [])
    cables.sort()

    let filteredCableLines = [...cableLines]

    let query = ''
    let room = ''
    let cable = ''
    function search() {
        if (query) {
            const normalizedQuery = query.toLowerCase()

            filteredCableLines = cableLines.filter((line) => {
                const inName = line.name.toLowerCase().indexOf(normalizedQuery) !== -1
                const inCable = line.cable.toLowerCase().indexOf(normalizedQuery) !== -1
                const inPoints = line.points.some((point) => {
                    const inPointCode = point.code.toLowerCase().indexOf(normalizedQuery) !== -1
                    const inPointName = point.name.toLowerCase().indexOf(normalizedQuery) !== -1

                    return inPointCode || inPointName
                })

                return inName || inCable || inPoints
            })
        } else {
            filteredCableLines = [...cableLines]
        }

        if (room) {
            filteredCableLines = filteredCableLines.filter((line) => room === line.room)
        }

        if (cable) {
            filteredCableLines = filteredCableLines.filter((line) => cable === line.cable)
        }
    }

    function isHighlight(point:any) {
        if (query) {
            return point.name.toLowerCase().includes(query.toLowerCase())
        }
    }
</script>

<div class="container">
    <div class="filter">
        <input bind:value={ query } type="text" placeholder="Поиск" on:input={ search }>
        <select bind:value={ room } on:change={search}>
            {#each rooms as room}
                <option>{ room }</option>
            {/each}
        </select>
        <select bind:value={ cable } on:change={search}>
            {#each cables as cable}
                <option>{ cable }</option>
            {/each}
        </select>
    </div>

    {#each filteredCableLines as line}
        <div class="line">
            <div class="line__name">{line.code} - { line.name } - { line.room }</div>
            <div class="line__cable">{ line.cable }</div>
            <div class="line__dots">
                {#each line.points as point}
                    <div class="line__point" class:highlight={isHighlight(point)}>
                        <span class="line__point-code">{ point.code }</span>
                        <span class="line__point-name">{ point.name }</span>
                    </div>
                {/each}
            </div>
        </div>
    {/each} 
</div>

<style>
    * {
        box-sizing: border-box;
    }

    .container {
        font-family: Arial, Helvetica, sans-serif;
        font-size: 18px;
        color: #313131;
        width: 600px;
        margin: 0 auto;
        display: flex;
        flex-direction: column;
        gap: 20px;
    }

    .filter {
        display: flex;
        flex-direction: column;
        gap: 10px;
    }

    input,
    select {
        padding: 5px 10px;
        border: 1px solid #bab8b8;
        border-radius: 5px;
        font-size: inherit;
        width: 100%;
    }

    .line {
        display: flex;
        flex-direction: column;
        gap: 5px;
    }

    .line__name {
        font-weight: 600;
    }

    .line__cable {
        color: #717171;
        font-size: 0.8em;
    }

    .line__dots {
        padding-left: 1em;
        font-size: 0.8em;
        display: flex;
        flex-direction: column;
        gap: 5px;
    }

    .highlight {
        background-color: #e7e586;
    }
</style>