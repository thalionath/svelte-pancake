<script>
    import * as Pancake from '@sveltejs/pancake';

    export let points = [];

    $: minx = points.length ? points[0].received : -Infinity;
    $: maxx = points.length ? points.slice(-1).pop().received : +Infinity;
    $: miny = points.length ? points[0].code : -Infinity;
    $: maxy = points.length ? points.slice(-1).pop().code : +Infinity;

    const format = timestamp => {
        const h =  Math.floor(timestamp / 3600e3) % 24
            , m =  Math.floor(timestamp / 60e3) % 60
            , s =  Math.floor(timestamp / 1e3) % 60
            , ms = timestamp % 1000
            ;
		return `${h}:${m}:${s}.${ms}`;
	};

	const pc = date => {
		return 100 * (date - minx) / (maxx - minx);
	};
</script>

<div class="chart">
    <Pancake.Chart x1={minx} x2={maxx} y1={miny} y2={maxy}>
        <Pancake.Grid horizontal count={5} let:value let:last>
			<div class="grid-line horizontal"><span>{value} {last ? 'adc' : ''}</span></div>
		</Pancake.Grid>

        <Pancake.Grid vertical count={5} let:value>
			<div class="grid-line vertical"></div>
			<span class="time-label">{value}</span>
		</Pancake.Grid>

        <Pancake.Svg>
            <Pancake.SvgScatterplot data={points} x="{d => d.received}" y="{d => d.code}" let:d>
                <path class="avg scatter" {d}/>
            </Pancake.SvgScatterplot>

            <Pancake.SvgLine data={points} x="{d => d.received}" y="{d => d.code}" let:d>
                <path class="avg" {d}/>
            </Pancake.SvgLine>
        </Pancake.Svg>

        <Pancake.Quadtree data={points} x="{d => d.received}" y="{d => d.code}" let:closest>
			{#if closest}
				<Pancake.Point x={closest.received} y={closest.code} let:d>
					<div class="focus"></div>
					<div class="tooltip" style="transform: translate(-{pc(closest.date)}%,0)">
						<strong>{closest.code} code</strong>
						<span>{format(closest.received)}</span>
					</div>
				</Pancake.Point>
			{/if}
		</Pancake.Quadtree>
    </Pancake.Chart>
</div>

<style>
	.chart {
		height: 450px;
		padding: 3em 0 2em 2em;
		margin: 0 0 36px 0;
		max-width: 80em;
		margin: 0 auto;
	}

    path.avg {
		stroke: #676778;
		opacity: 0.5;
		stroke-linejoin: round;
		stroke-linecap: round;
		stroke-width: 1px;
		fill: none;
	}

	path.scatter {
		stroke-width: 3px;
	}

    .grid-line {
		position: relative;
		display: block;
	}

	.grid-line.horizontal {
		width: calc(100% + 2em);
		left: -2em;
		border-bottom: 1px dashed #ccc;
	}

	.grid-line.vertical {
		height: 100%;
		border-left: 1px dashed #ccc;
	}

    .time-label {
		position: absolute;
		width: 4em;
		left: -2em;
		bottom: -30px;
		font-family: sans-serif;
		font-size: 14px;
		color: #999;
		text-align: center;
	}

    .focus {
		position: absolute;
		width: 10px;
		height: 10px;
		left: -5px;
		top: -5px;
		border: 1px solid black;
		border-radius: 50%;
		box-sizing: border-box;
	}

    .tooltip {
		position: absolute;
		white-space: nowrap;
		width: 8em;
		bottom: 1em;
		/* background-color: white; */
		line-height: 1;
		text-shadow: 0 0 10px white, 0 0 10px white, 0 0 10px white, 0 0 10px white, 0 0 10px white, 0 0 10px white, 0 0 10px white;
	}

	.tooltip strong {
		font-size: 1.4em;
		display: block;
	}
</style>