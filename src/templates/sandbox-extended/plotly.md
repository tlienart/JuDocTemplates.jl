@def title = "Franklin + Plotly"
@def hascode = true
@def hasplotly = true <!-- see config.md and _layout/head.html -->

# Franklin + Plotly

* [back home](/)

The convenience function `fdplotly` helps to extract the relevant HTML from a plotly plot via `json` (don't forget `json`! see examples); you can then place it using `\textoutput` (see examples). The `fdplotly` function takes two optional keyword arguments:

* `id`: the name of the div in which the plot should be placed (randomly generated by default),
* `style`: a CSS string specifying the style of the div, by default `"width:600px;height:350px"`

We show some examples drawn from the [PlotlyJS examples](https://github.com/sglyon/PlotlyJS.jl/tree/master/examples).

```julia:init
# hideall
using PlotlyJS
```

## Examples

### Line scatter

[Original file](https://github.com/sglyon/PlotlyJS.jl/blob/master/examples/line_scatter.jl)

```julia:lines_pre
#hideall
function linescatter1()
    trace1 = scatter(;x=1:4, y=[10, 15, 13, 17], mode="markers")
    trace2 = scatter(;x=2:5, y=[16, 5, 11, 9], mode="lines")
    trace3 = scatter(;x=1:4, y=[12, 9, 15, 12], mode="lines+markers")
    plot([trace1, trace2, trace3])
end
function batman()
    σ(x) = @. √(1-x^2)
    el(x) = @. 3*σ(x/7)
    s(x) = @. 4.2 - 0.5*x - 2.0*σ(0.5*x-0.5)
    b(x) = @. σ(abs(2-x)-1) - x^2/11 + 0.5x - 3
    c(x) = [1.7, 1.7, 2.6, 0.9]

    p(i, f; kwargs...) = scatter(; x=[-i; 0.0; i], y=[f(i); NaN; f(i)],
                                  marker_color="black", showlegend=false,
                                  kwargs...)
    traces = vcat(p(3:0.1:7, el;         name="wings 1"),
                  p(4:0.1:7, t->-el(t);  name="wings 2"),
                  p(1:0.1:3, s;          name="Shoulders"),
                  p(0:0.1:4, b;          name="Bottom"),
                  p([0, 0.5, 0.8, 1], c; name="head"))

    plot(traces, Layout(title="Batman"))
end
```

```julia:lines1
plt = linescatter1()
fdplotly(json(plt), style="width:400px;height:250px;margin-left:auto;margin-right:auto;")
```
\textoutput{lines1}

```julia:batman
plt = batman()
fdplotly(json(plt))
```
\textoutput{batman}

### Ternary

[Original file](https://github.com/sglyon/PlotlyJS.jl/blob/master/examples/ternary.jl)

```julia:ternary
#hideall
function make_ax(title, tickangle)
    attr(title=title, titlefont_size=20, tickangle=tickangle,
        tickfont_size=15, tickcolor="rgba(0, 0, 0, 0)", ticklen=5,
        showline=true, showgrid=true)
end

raw_data = [
    Dict(:journalist=>75, :developer=>:25, :designer=>0, :label=>"point 1"),
    Dict(:journalist=>70, :developer=>:10, :designer=>20, :label=>"point 2"),
    Dict(:journalist=>75, :developer=>:20, :designer=>5, :label=>"point 3"),
    Dict(:journalist=>5, :developer=>:60, :designer=>35, :label=>"point 4"),
    Dict(:journalist=>10, :developer=>:80, :designer=>10, :label=>"point 5"),
    Dict(:journalist=>10, :developer=>:90, :designer=>0, :label=>"point 6"),
    Dict(:journalist=>20, :developer=>:70, :designer=>10, :label=>"point 7"),
    Dict(:journalist=>10, :developer=>:20, :designer=>70, :label=>"point 8"),
    Dict(:journalist=>15, :developer=>:5, :designer=>80, :label=>"point 9"),
    Dict(:journalist=>10, :developer=>:10, :designer=>80, :label=>"point 10"),
    Dict(:journalist=>20, :developer=>:10, :designer=>70, :label=>"point 11")
]

t = scatterternary(
    mode="markers",
    a=[d[:journalist] for d in raw_data],
    b=[d[:developer] for d in raw_data],
    c=[d[:designer] for d in raw_data],
    text=[d[:label] for d in raw_data],
    marker=attr(symbol=100, color="#DB7365", size=14, line_width=2)
)
layout = Layout(
    ternary=attr(
        sum=100,
        aaxis=make_ax("Journalist", 0),
        baxis=make_ax("Developer", 45),
        caxis=make_ax("Designer", -45),
        bgcolor="#fff1e0",
    ), annotations=attr(
        showarrow=false,
        text="Replica of Tom Pearson's block",
        x=1.0, y=1.3, font_size=15
    ),
    paper_bgcolor="#fff1e0"
)
plt = plot(t, layout)

fdplotly(json(plt))
```

\textoutput{ternary}