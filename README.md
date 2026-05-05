<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Stochastic Analysis of Project Completion Time</title>
    <script>
        window.MathJax = {
            tex: {
                inlineMath: [['$', '$']],
                displayMath: [['$$', '$$']],
                tags: 'ams'
            }
        };
    </script>
    <script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" async></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.4/dist/chart.umd.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-annotation@3.0.1/dist/chartjs-plugin-annotation.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/plotly.js-gl3d-dist@2.35.0/plotly-gl3d.min.js"></script>
    <style>
        :root {
            --bg: #fafafa;
            --fg: #222;
            --accent: #1a5276;
            --border: #ccc;
            --code-bg: #f0f0f0;
            --highlight: #d4e6f1;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            font-family: "Palatino Linotype", "Book Antiqua", Palatino, Georgia, serif;
            background: var(--bg);
            color: var(--fg);
            line-height: 1.75;
            max-width: 820px;
            margin: 0 auto;
            padding: 40px 24px 80px;
        }

        h1 {
            font-size: 1.9em;
            text-align: center;
            margin-bottom: 4px;
            color: var(--accent);
        }

        .authors {
            text-align: center;
            font-style: italic;
            margin-bottom: 6px;
            color: #555;
        }

        .date {
            text-align: center;
            margin-bottom: 30px;
            color: #777;
            font-size: 0.95em;
        }

        .abstract {
            background: var(--highlight);
            border-left: 4px solid var(--accent);
            padding: 16px 20px;
            margin-bottom: 32px;
            font-size: 0.97em;
        }

        .abstract strong { display: block; margin-bottom: 4px; }

        h2 {
            font-size: 1.35em;
            color: var(--accent);
            margin-top: 36px;
            margin-bottom: 12px;
            border-bottom: 1px solid var(--border);
            padding-bottom: 4px;
        }

        h3 {
            font-size: 1.1em;
            color: #2c3e50;
            margin-top: 22px;
            margin-bottom: 8px;
        }

        p { margin-bottom: 12px; text-align: justify; }

        ul, ol {
            margin: 8px 0 12px 28px;
        }

        li { margin-bottom: 4px; }

        table {
            width: 100%;
            border-collapse: collapse;
            margin: 16px 0;
            font-size: 0.95em;
        }

        th {
            background: var(--accent);
            color: #fff;
            padding: 8px 10px;
            text-align: left;
        }

        td {
            border: 1px solid var(--border);
            padding: 7px 10px;
        }

        tr:nth-child(even) { background: #f4f4f4; }

        .boxed {
            border: 2px solid var(--accent);
            background: #eaf2f8;
            padding: 14px 18px;
            margin: 18px 0;
            border-radius: 4px;
        }

        .boxed-title {
            font-weight: bold;
            color: var(--accent);
            margin-bottom: 6px;
        }

        .definition {
            border-left: 4px solid #27ae60;
            background: #eafaf1;
            padding: 12px 16px;
            margin: 14px 0;
        }

        .theorem {
            border-left: 4px solid #8e44ad;
            background: #f5eef8;
            padding: 12px 16px;
            margin: 14px 0;
        }

        .proof {
            border-left: 4px solid #999;
            background: #f9f9f9;
            padding: 12px 16px;
            margin: 14px 0;
            font-size: 0.96em;
        }

        .proof::before {
            content: "Proof. ";
            font-weight: bold;
            font-style: italic;
        }

        .qed {
            text-align: right;
            font-size: 1.1em;
        }

        pre {
            background: var(--code-bg);
            border: 1px solid var(--border);
            padding: 14px;
            overflow-x: auto;
            font-family: "Consolas", "Courier New", monospace;
            font-size: 0.88em;
            margin: 14px 0;
            border-radius: 3px;
            line-height: 1.5;
        }

        .footnote {
            font-size: 0.85em;
            color: #666;
            border-top: 1px solid var(--border);
            margin-top: 40px;
            padding-top: 12px;
        }

        a { color: var(--accent); }

        /* Interactive Chart Styles */
        .interactive-section {
            background: #fff;
            border: 2px solid var(--accent);
            border-radius: 8px;
            padding: 24px;
            margin: 28px 0 36px;
            box-shadow: 0 2px 12px rgba(0,0,0,0.07);
        }
        .interactive-section h3 {
            color: var(--accent);
            font-size: 1.2em;
            margin: 0 0 16px;
            text-align: center;
        }
        .param-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px 24px;
            margin-bottom: 18px;
        }
        .param-row {
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .param-row label {
            min-width: 90px;
            font-size: 0.92em;
            font-weight: 600;
            color: #333;
            white-space: nowrap;
            font-family: "Consolas", monospace;
        }
        .param-row input[type="range"] {
            flex: 1;
            accent-color: var(--accent);
            cursor: pointer;
        }
        .param-row .param-val {
            min-width: 44px;
            text-align: right;
            font-family: "Consolas", monospace;
            font-size: 0.9em;
            color: var(--accent);
            font-weight: bold;
        }
        .chart-container {
            position: relative;
            width: 100%;
            height: 370px;
            margin-top: 8px;
        }
        .stats-bar {
            display: flex;
            justify-content: center;
            gap: 32px;
            margin-top: 12px;
            font-size: 0.93em;
            flex-wrap: wrap;
        }
        .stats-bar span {
            font-family: "Consolas", monospace;
            padding: 4px 12px;
            border-radius: 4px;
        }
        .stat-mean { background: #fdebd0; border: 1px solid #e67e22; }
        .stat-ci { background: #d5f5e3; border: 1px solid #27ae60; }
        .stat-std { background: #d4e6f1; border: 1px solid #2980b9; }

        /* Sensitivity Charts */
        .sensitivity-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 24px;
            margin: 24px 0;
        }
        .sens-card {
            background: #fff;
            border: 1px solid var(--border);
            border-radius: 6px;
            padding: 14px 16px 10px;
            box-shadow: 0 1px 6px rgba(0,0,0,0.05);
        }
        .sens-card h4 {
            text-align: center;
            color: var(--accent);
            font-size: 0.98em;
            margin: 0 0 8px;
        }
        .sens-chart-wrap {
            position: relative;
            width: 100%;
            height: 220px;
        }
        .sens-defaults {
            text-align: center;
            font-size: 0.88em;
            color: #666;
            margin-bottom: 8px;
            font-family: "Consolas", monospace;
        }
        @media (max-width: 700px) {
            .sensitivity-grid { grid-template-columns: 1fr; }
        }

        /* 3D Surface Chart */
        .surface-section {
            background: #fff;
            border: 2px solid var(--accent);
            border-radius: 8px;
            padding: 20px 18px;
            margin: 28px 0;
            box-shadow: 0 2px 12px rgba(0,0,0,0.07);
        }
        .surface-section h4 {
            text-align: center;
            color: var(--accent);
            font-size: 1.08em;
            margin: 0 0 6px;
        }
        .surface-section .surface-desc {
            text-align: center;
            font-size: 0.88em;
            color: #666;
            margin-bottom: 10px;
            font-family: "Consolas", monospace;
        }
        #surface3d {
            width: 100%;
            height: 520px;
        }
        .surface-params {
            display: flex;
            justify-content: center;
            gap: 18px;
            flex-wrap: wrap;
            margin-bottom: 12px;
        }
        .surface-params .param-row {
            min-width: 200px;
        }
    </style>
</head>
<body>

<h1>Stochastic Analysis of Project Completion Time:<br>A Markov Chain Approach</h1>
<div class="authors"> </div>
<div class="date">May 4, 2026</div>

<div class="abstract">
    <strong>Abstract.</strong>
    We consider a project management model in which $N$ issues must be discovered and resolved
    by $m$ identical workers. Workers stochastically find unknown issues or resolve known ones,
    governed by probabilities $p_f$, $p_r$ and an intention parameter $I_{rf}$.
    We formalize the system as a two-dimensional absorbing Markov chain, derive a closed-form
    approximation for the expected completion time $\mathbb{E}[T]$, characterize the asymptotic
    distribution of $T$, prove the optimal worker-allocation rule, and analyze parameter sensitivity.
</div>

<!-- Interactive Distribution Chart -->
<div class="interactive-section">
    <h3>Interactive Explorer: Distribution of Completion Time <em>T</em></h3>
    <div class="param-grid">
        <div class="param-row">
            <label>N =</label>
            <input type="range" id="sl-N" min="10" max="500" step="5" value="100">
            <span class="param-val" id="val-N">100</span>
        </div>
        <div class="param-row">
            <label>S₀ =</label>
            <input type="range" id="sl-S0" min="0.01" max="0.99" step="0.01" value="0.20">
            <span class="param-val" id="val-S0">0.20</span>
        </div>
        <div class="param-row">
            <label>m =</label>
            <input type="range" id="sl-m" min="1" max="50" step="1" value="5">
            <span class="param-val" id="val-m">5</span>
        </div>
        <div class="param-row">
            <label>p_r =</label>
            <input type="range" id="sl-pr" min="0.01" max="1.00" step="0.01" value="0.30">
            <span class="param-val" id="val-pr">0.30</span>
        </div>
        <div class="param-row">
            <label>p_f =</label>
            <input type="range" id="sl-pf" min="0.01" max="1.00" step="0.01" value="0.20">
            <span class="param-val" id="val-pf">0.20</span>
        </div>
        <div class="param-row">
            <label>I_rf =</label>
            <input type="range" id="sl-Irf" min="0.01" max="0.99" step="0.01" value="0.40">
            <span class="param-val" id="val-Irf">0.40</span>
        </div>
    </div>
    <div class="chart-container">
        <canvas id="distChart"></canvas>
    </div>
    <div class="stats-bar">
        <span class="stat-mean" id="stat-mean">E[T] = —</span>
        <span class="stat-ci" id="stat-ci">95% CI: — – —</span>
        <span class="stat-std" id="stat-std">σ = —</span>
    </div>
</div>

<script>
(function () {
    // Monte Carlo simulation of T
    function simulateT(N, S0, m, pr, pf, Irf, rng) {
        let K = Math.floor(S0 * N);
        let R = 0;
        let t = 0;
        while (R < N && t < 100000) {
            const A = K - R;
            const U = N - K;
            let mFindIntent = U > 0 ? Math.round(Irf * m) : 0;
            let mResolveIntent = m - mFindIntent;
            let mR, mF;
            if (mResolveIntent > A) {
                const overflow = mResolveIntent - A;
                mR = A;
                mF = U > 0 ? Math.min(mFindIntent + overflow, m) : 0;
            } else {
                mR = mResolveIntent;
                mF = U > 0 ? mFindIntent : 0;
            }
            let dF = 0, dR = 0;
            for (let i = 0; i < mF; i++) { if (rng() < pf) dF++; }
            for (let i = 0; i < mR; i++) { if (rng() < pr) dR++; }
            K += Math.min(dF, U);
            R += Math.min(dR, A);
            t++;
        }
        return t;
    }

    // Seeded PRNG (xoshiro128**)
    function makeRng(seed) {
        let s = [seed | 0, (seed * 1664525 + 1013904223) | 0,
                 (seed * 214013 + 2531011) | 0, (seed * 1103515245 + 12345) | 0];
        function rotl(x, k) { return ((x << k) | (x >>> (32 - k))) >>> 0; }
        return function () {
            const result = (rotl((s[1] * 5) >>> 0, 7) * 9) >>> 0;
            const t = (s[1] << 9) >>> 0;
            s[2] ^= s[0]; s[3] ^= s[1]; s[1] ^= s[2]; s[0] ^= s[3];
            s[2] ^= t; s[3] = rotl(s[3], 11);
            return (result >>> 0) / 4294967296;
        };
    }

    // Build histogram from samples
    function buildHistogram(samples) {
        const minV = Math.min(...samples);
        const maxV = Math.max(...samples);
        const range = maxV - minV || 1;
        const nBins = Math.min(80, Math.max(20, Math.ceil(Math.sqrt(samples.length))));
        const binW = range / nBins;
        const bins = new Array(nBins).fill(0);
        const labels = [];
        for (let i = 0; i < nBins; i++) {
            labels.push(Math.round(minV + (i + 0.5) * binW));
        }
        for (const s of samples) {
            let idx = Math.floor((s - minV) / binW);
            if (idx >= nBins) idx = nBins - 1;
            bins[idx]++;
        }
        // Normalize to density
        const total = samples.length * binW;
        const density = bins.map(b => b / total);
        return { labels, density, binW, minV };
    }

    // Compute statistics
    function stats(samples) {
        const n = samples.length;
        const sorted = [...samples].sort((a, b) => a - b);
        const mean = samples.reduce((a, b) => a + b, 0) / n;
        const variance = samples.reduce((a, b) => a + (b - mean) ** 2, 0) / (n - 1);
        const std = Math.sqrt(variance);
        const lo = sorted[Math.floor(n * 0.025)];
        const hi = sorted[Math.floor(n * 0.975)];
        return { mean, std, lo, hi };
    }

    // Chart setup
    const ctx = document.getElementById('distChart').getContext('2d');
    let chart = null;

    function renderChart() {
        const N   = +document.getElementById('sl-N').value;
        const S0  = +document.getElementById('sl-S0').value;
        const m   = +document.getElementById('sl-m').value;
        const pr  = +document.getElementById('sl-pr').value;
        const pf  = +document.getElementById('sl-pf').value;
        const Irf = +document.getElementById('sl-Irf').value;

        // Update displayed values
        document.getElementById('val-N').textContent = N;
        document.getElementById('val-S0').textContent = S0.toFixed(2);
        document.getElementById('val-m').textContent = m;
        document.getElementById('val-pr').textContent = pr.toFixed(2);
        document.getElementById('val-pf').textContent = pf.toFixed(2);
        document.getElementById('val-Irf').textContent = Irf.toFixed(2);

        // Run Monte Carlo (adaptive sample count)
        const nSim = N > 200 ? 2000 : 4000;
        const rng = makeRng(42);
        const samples = [];
        for (let i = 0; i < nSim; i++) {
            samples.push(simulateT(N, S0, m, pr, pf, Irf, rng));
        }

        const st = stats(samples);
        const hist = buildHistogram(samples);

        // Mark 95% region
        const bgColors = hist.labels.map(l => {
            return (l >= st.lo && l <= st.hi)
                ? 'rgba(39, 174, 96, 0.45)'
                : 'rgba(26, 82, 118, 0.30)';
        });
        const borderColors = hist.labels.map(l => {
            return (l >= st.lo && l <= st.hi)
                ? 'rgba(39, 174, 96, 0.8)'
                : 'rgba(26, 82, 118, 0.5)';
        });

        // Update stats bar
        document.getElementById('stat-mean').textContent =
            'E[T] ≈ ' + st.mean.toFixed(1) + ' days';
        document.getElementById('stat-ci').textContent =
            '95% interval: [' + st.lo + ', ' + st.hi + ']';
        document.getElementById('stat-std').textContent =
            'σ ≈ ' + st.std.toFixed(1);

        if (chart) chart.destroy();
        chart = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: hist.labels,
                datasets: [{
                    label: 'Density of T',
                    data: hist.density,
                    backgroundColor: bgColors,
                    borderColor: borderColors,
                    borderWidth: 1,
                    barPercentage: 1.0,
                    categoryPercentage: 1.0
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                animation: { duration: 250 },
                scales: {
                    x: {
                        title: { display: true, text: 'Completion Time T (days)', font: { size: 13 } },
                        ticks: {
                            maxTicksLimit: 15,
                            font: { size: 10 }
                        }
                    },
                    y: {
                        title: { display: true, text: 'Probability Density', font: { size: 13 } },
                        beginAtZero: true,
                        ticks: { font: { size: 10 } }
                    }
                },
                plugins: {
                    legend: { display: false },
                    tooltip: {
                        callbacks: {
                            title: (items) => 'T ≈ ' + items[0].label + ' days',
                            label: (item) => 'density: ' + (+item.raw).toFixed(5)
                        }
                    },
                    annotation: {
                        annotations: {
                            meanLine: {
                                type: 'line',
                                xMin: findLabelIndex(hist.labels, st.mean),
                                xMax: findLabelIndex(hist.labels, st.mean),
                                borderColor: '#e67e22',
                                borderWidth: 2.5,
                                borderDash: [6, 3],
                                label: {
                                    display: true,
                                    content: 'E[T]=' + st.mean.toFixed(1),
                                    position: 'start',
                                    backgroundColor: '#e67e22',
                                    color: '#fff',
                                    font: { size: 11, weight: 'bold' },
                                    padding: 4
                                }
                            },
                            ciLo: {
                                type: 'line',
                                xMin: findLabelIndex(hist.labels, st.lo),
                                xMax: findLabelIndex(hist.labels, st.lo),
                                borderColor: '#27ae60',
                                borderWidth: 1.5,
                                borderDash: [4, 4],
                                label: {
                                    display: true,
                                    content: '2.5%',
                                    position: 'start',
                                    backgroundColor: '#27ae60',
                                    color: '#fff',
                                    font: { size: 10 },
                                    padding: 3
                                }
                            },
                            ciHi: {
                                type: 'line',
                                xMin: findLabelIndex(hist.labels, st.hi),
                                xMax: findLabelIndex(hist.labels, st.hi),
                                borderColor: '#27ae60',
                                borderWidth: 1.5,
                                borderDash: [4, 4],
                                label: {
                                    display: true,
                                    content: '97.5%',
                                    position: 'start',
                                    backgroundColor: '#27ae60',
                                    color: '#fff',
                                    font: { size: 10 },
                                    padding: 3
                                }
                            }
                        }
                    }
                }
            }
        });
    }

    function findLabelIndex(labels, value) {
        let best = 0, bestDist = Infinity;
        for (let i = 0; i < labels.length; i++) {
            const d = Math.abs(labels[i] - value);
            if (d < bestDist) { bestDist = d; best = i; }
        }
        return best;
    }

    // Debounced update
    let timer = null;
    function onSliderChange() {
        clearTimeout(timer);
        // Immediately update displayed value
        const id = this.id.replace('sl-', 'val-');
        const v = +this.value;
        document.getElementById(id).textContent =
            (this.step.includes('.') ? v.toFixed(2) : v.toString());
        timer = setTimeout(renderChart, 150);
    }

    document.querySelectorAll('.param-row input[type="range"]').forEach(sl => {
        sl.addEventListener('input', onSliderChange);
    });

    // Initial render
    renderChart();
})();
</script>

<!-- ====================================================================== -->
<h2>1. Introduction and Notation</h2>

<p>
    Project management often involves two concurrent activities: <em>discovering</em> what work
    remains and <em>executing</em> known work items. We model a project containing a fixed total
    of $N$ issues, not all of which are known at the outset. A team of $m$ workers operates in
    discrete daily rounds, each worker independently attempting either to find an unknown issue
    or to resolve a known one.
</p>

<p>We introduce the following symbols, which remain fixed throughout the paper:</p>

<div class="definition">
    <strong>Definition 1.1 (Model Parameters).</strong>
    <ul>
        <li>$N \in \mathbb{Z}^+$ &mdash; total number of issues in the project (fixed, known to the manager).</li>
        <li>$S_0 \in (0, 1]$ &mdash; initial discovery ratio; $K(0) = \lfloor S_0 N \rfloor$ issues are known at day $t = 0$.</li>
        <li>$m \in \mathbb{Z}^+$ &mdash; number of identical workers.</li>
        <li>$p_r \in (0, 1]$ &mdash; probability that a worker resolves an assigned issue in one day.</li>
        <li>$p_f \in (0, 1]$ &mdash; probability that a worker discovers a new unknown issue in one day.</li>
        <li>$I_{rf} \in (0, 1)$ &mdash; intention switch; the fraction of workers that <em>prefer</em> finding over resolving.</li>
    </ul>
</div>

<div class="definition">
    <strong>Definition 1.2 (State Variables at Day $t$).</strong>
    <ul>
        <li>$K(t)$ &mdash; cumulative number of <strong>known</strong> (discovered) issues, $K(0) = \lfloor S_0 N \rfloor$.</li>
        <li>$R(t)$ &mdash; cumulative number of <strong>resolved</strong> issues, $R(0) = 0$.</li>
        <li>$U(t) = N - K(t)$ &mdash; number of <strong>unknown</strong> (undiscovered) issues.</li>
        <li>$A(t) = K(t) - R(t)$ &mdash; number of <strong>active</strong> (known but unresolved) issues.</li>
    </ul>
</div>

<div class="definition">
    <strong>Definition 1.3 (Completion Time).</strong>
    $$T = \min\{t \in \mathbb{Z}^+ : R(t) = N\}.$$
    The project is finished when <em>all</em> $N$ issues have been both found and resolved.
</div>

<!-- ====================================================================== -->
<h2>2. The Markov Chain Model</h2>

<h3>2.1 Worker Allocation Rule</h3>

<p>
    At the start of each day $t$, workers are allocated to tasks. Let $m_F(t)$ and $m_R(t)$
    denote the number of workers assigned to finding and resolving, respectively.
    The intention parameter $I_{rf}$ determines the <em>preferred</em> split, but the
    parallelism constraint — a resolver needs an active issue to work on — may override it.
</p>

<div class="boxed">
    <div class="boxed-title">Allocation Rule.</div>
    Let $A = A(t)$ and $U = U(t)$. Define:
    $$
    m_F^{\text{intent}} = \begin{cases} \lceil I_{rf} \cdot m \rceil & \text{if } U > 0, \\ 0 & \text{if } U = 0. \end{cases}
    $$
    $$
    m_R^{\text{intent}} = m - m_F^{\text{intent}}.
    $$
    If $m_R^{\text{intent}} > A$, the excess $m_R^{\text{intent}} - A$ workers cannot resolve
    (no available active issues) and are <strong>reassigned to finding</strong> if $U > 0$,
    otherwise they idle. Thus:
    $$
    m_R(t) = \min\!\big(A(t),\; m - m_F^{\text{intent}}\big),
    $$
    $$
    m_F(t) = \min\!\big(m - m_R(t),\; m\big) \cdot \mathbb{1}[U(t) > 0].
    $$
</div>

<h3>2.2 Daily Transitions</h3>

<p>
    Given state $(K, R)$ at day $t$, the increments are independent binomial random variables:
</p>

$$
\Delta F(t) \sim \text{Binomial}\!\big(m_F(t),\; p_f\big), \qquad
\Delta R(t) \sim \text{Binomial}\!\big(m_R(t),\; p_r\big).
$$

<p>Updates (with natural capping):</p>

$$
K(t+1) = K(t) + \min\!\big(\Delta F(t),\; U(t)\big), \qquad
R(t+1) = R(t) + \min\!\big(\Delta R(t),\; A(t)\big).
$$

<p>
    The capping $\min(\Delta R, A)$ is automatically satisfied since $m_R \le A$ implies
    $\Delta R \le m_R \le A$. Similarly, $\Delta F$ could exceed $U$ only when $m_F > U$;
    we cap to maintain physical validity.
</p>

<div class="theorem">
    <strong>Proposition 2.1.</strong>
    The process $\{(K(t), R(t))\}_{t \ge 0}$ is a time-homogeneous absorbing Markov chain
    on the finite state space
    $$
    \mathcal{S} = \{(K, R) \in \mathbb{Z}^2 : 0 \le R \le K \le N\}
    $$
    with unique absorbing state $(N, N)$.
</div>

<div class="proof">
    The transition probabilities from $(K, R)$ depend only on $(K, R)$ (since $m_F, m_R$
    are deterministic functions of $A = K - R$ and $U = N - K$), establishing the Markov property.
    The state $(N, N)$ is absorbing since $U = 0, A = 0$ implies $m_F = m_R = 0$ and no
    transitions occur. It is the <em>unique</em> absorbing state because from any other state
    with $R < N$, either $A > 0$ (progress via resolve) or $U > 0$ (progress via find),
    each with positive probability.
    <div class="qed">$\blacksquare$</div>
</div>

<!-- ====================================================================== -->
<h2>3. Mean-Field (Deterministic) Approximation</h2>

<p>
    To gain analytical insight, we replace random increments by their expectations and treat
    time as continuous. Let $k(t)$ and $r(t)$ denote continuous analogues of $K(t)$ and $R(t)$.
    Define $a(t) = k(t) - r(t)$ and $u(t) = N - k(t)$.
</p>

<h3>3.1 Phase 1: Worker-Bottlenecked ($a(t) < m$)</h3>

<p>
    When active issues are scarce, all $a$ active issues get a resolver, and the remaining
    $m - a$ workers find. The system of ODEs is:
</p>

$$
\dot{k} = (m - a)\, p_f, \qquad \dot{r} = a\, p_r.
$$

<p>Subtracting to get the active-issue dynamics:</p>

$$
\dot{a} = \dot{k} - \dot{r} = (m - a)\, p_f - a\, p_r = m\, p_f - a(p_f + p_r).
$$

<div class="theorem">
    <strong>Proposition 3.1 (Active-Issue Dynamics in Phase 1).</strong>
    The linear ODE $\dot{a} = m p_f - a(p_f + p_r)$ with initial condition $a(0) = a_0$
    has solution:
    $$
    a(t) = a^\star + (a_0 - a^\star)\, e^{-(p_f + p_r)\, t},
    $$
    where
    $$
    a^\star = \frac{m\, p_f}{p_f + p_r}
    $$
    is the stable equilibrium.
</div>

<div class="proof">
    This is a first-order linear ODE of the form $\dot{a} + \lambda a = c$ with
    $\lambda = p_f + p_r$ and $c = m p_f$. The integrating factor is $e^{\lambda t}$.
    Multiplying both sides:
    $$\frac{d}{dt}\!\left[a\, e^{\lambda t}\right] = c\, e^{\lambda t}.$$
    Integrating:
    $$a\, e^{\lambda t} = \frac{c}{\lambda}\, e^{\lambda t} + C, \qquad
    a(t) = \frac{c}{\lambda} + C\, e^{-\lambda t}.$$
    Applying $a(0) = a_0$ gives $C = a_0 - c/\lambda = a_0 - a^\star$.
    <div class="qed">$\blacksquare$</div>
</div>

<p>
    <strong>Interpretation:</strong> The active backlog exponentially converges to
    $a^\star = m p_f / (p_f + p_r)$. If $a_0 < a^\star$, the backlog grows; if $a_0 > a^\star$,
    it shrinks. Phase 1 ends when $a(t) \ge m$ or when we enter the steady regime.
</p>

<h3>3.2 Phase 2: Steady Parallel Work ($a(t) \ge m$, $u(t) > 0$)</h3>

<p>
    When enough active issues exist, the intention switch governs allocation directly:
    $m_F = I_{rf}\, m$ and $m_R = (1 - I_{rf})\, m$. The ODEs become linear:
</p>

$$
\dot{k} = I_{rf}\, m\, p_f, \qquad \dot{r} = (1 - I_{rf})\, m\, p_r.
$$

<p>
    This phase ends when $k(t) = N$, i.e., all issues are discovered. The time for this
    phase (measuring from its start, where $k = k_1$) is:
</p>

$$
t_{\text{find}} = \frac{N - k_1}{I_{rf}\, m\, p_f}.
$$

<h3>3.3 Phase 3: Resolve-Only ($u(t) = 0$, $a(t) > 0$)</h3>

<p>
    After all issues are found, all $m$ workers resolve. The remaining active issues
    drain at rate $m p_r$:
</p>

$$
\dot{r} = m\, p_r, \qquad t_{\text{resolve}} = \frac{N - r(t_{\text{find}})}{m\, p_r}.
$$

<h3>3.4 Approximate Mean Completion Time</h3>

<p>
    Combining Phases 2 and 3 (assuming Phase 1 is short when $S_0 N \gg 1$), the total
    resolve work is $N$ issues at a rate that is $(1 - I_{rf}) m p_r$ during Phase 2 and
    $m p_r$ during Phase 3. The find constraint and the resolve constraint must both be met,
    so:
</p>

<div class="boxed">
    <div class="boxed-title">Result 3.2: Approximate Mean Completion Time.</div>
    When $S_0 N \ge m$ (no Phase 1 bottleneck):
    $$
    \boxed{\;\mathbb{E}[T] \;\approx\; \frac{(1 - S_0)\, N}{I_{rf}\, m\, p_f}
    \;+\; \frac{N}{m\, p_r}\;}
    $$
    The first term is the time for all discoveries; the second is the time for all resolutions
    (dominated by the resolve-only tail phase).
</div>

<!-- ====================================================================== -->
<h2>4. Distribution of $T$</h2>

<h3>4.1 Exact Characterization</h3>

<p>
    Let $\mathbf{P}$ be the $(|\mathcal{S}| \times |\mathcal{S}|)$ transition matrix of
    the Markov chain. Partition $\mathcal{S} = \mathcal{T} \cup \{(N,N)\}$ where $\mathcal{T}$
    is the set of transient states. In canonical form:
</p>

$$
\mathbf{P} = \begin{pmatrix} \mathbf{Q} & \mathbf{r} \\ \mathbf{0}^T & 1 \end{pmatrix},
$$

<p>
    where $\mathbf{Q}$ is the transient-to-transient sub-matrix and $\mathbf{r}$ is the
    absorption vector. The distribution of completion time is:
</p>

$$
\Pr[T = t] = \mathbf{e}_{(K_0, 0)}^T \, \mathbf{Q}^{t-1} \, \mathbf{r},
$$

<p>
    where $\mathbf{e}_{(K_0,0)}$ is the indicator vector for the initial state
    $(K_0, 0) = (\lfloor S_0 N \rfloor, 0)$.
</p>

<p>
    The <strong>fundamental matrix</strong> $\mathbf{N} = (\mathbf{I} - \mathbf{Q})^{-1}$
    gives the expected number of visits to each transient state, and:
</p>

$$
\mathbb{E}[T] = \mathbf{e}_{(K_0,0)}^T \, \mathbf{N} \, \mathbf{1}.
$$

<h3>4.2 Asymptotic Normality (CLT Argument)</h3>

<div class="theorem">
    <strong>Theorem 4.1 (Asymptotic Distribution of $T$).</strong>
    As $N \to \infty$ with $m$, $p_r$, $p_f$, $I_{rf}$, $S_0$ fixed, the standardized
    completion time converges in distribution:
    $$
    \frac{T - \mu_T}{\sigma_T} \xrightarrow{d} \mathcal{N}(0, 1),
    $$
    where
    $$
    \mu_T = \frac{(1 - S_0)\, N}{I_{rf}\, m\, p_f} + \frac{N}{m\, p_r},
    $$
    $$
    \sigma_T^2 = \frac{(1 - S_0)\, N\, (1 - I_{rf}\, p_f)}{(I_{rf}\, m\, p_f)^2}
    + \frac{N\, (1 - p_r)}{(m\, p_r)^2}.
    $$
</div>

<div class="proof">
    <strong>Sketch.</strong> The completion time decomposes into two dominant components:
    <br><br>
    <em>(i) Discovery time $T_F$:</em> The number of days until all $N - K_0$ unknown issues
    are found. In each day of Phase 2, the number of new discoveries is
    $\text{Binomial}(m_F, p_f)$ with mean $\lambda_f = I_{rf}\, m\, p_f$.
    The total find time is approximately a sum of $\lceil (N - K_0) / \lambda_f \rceil$
    geometrically-distributed waiting times. By the CLT for renewal processes:
    $$
    T_F \approx \mathcal{N}\!\left(\frac{(1-S_0)N}{\lambda_f},\;
    \frac{(1-S_0)N\,(1 - I_{rf} p_f)}{\lambda_f^2}\right).
    $$

    <em>(ii) Resolution time $T_R$:</em> Similarly, resolving $N$ issues at rate
    $\lambda_r = m\, p_r$ (in the final phase) yields:
    $$
    T_R \approx \mathcal{N}\!\left(\frac{N}{\lambda_r},\;
    \frac{N(1 - p_r)}{\lambda_r^2}\right).
    $$

    Since $T \approx T_F + T_R'$ (where $T_R'$ is the residual resolve time after
    all discoveries) and the dominant fluctuations in the two phases are asymptotically
    independent (they occur in disjoint time windows for large $N$), the sum inherits
    normality by the CLT.
    <div class="qed">$\blacksquare$</div>
</div>

<p>
    <strong>Remark.</strong> For small $N$ or extreme parameter values, the distribution of
    $T$ is right-skewed. In such cases, a <strong>log-normal</strong> approximation or direct
    Monte Carlo simulation is more appropriate.
</p>

<!-- ====================================================================== -->
<h2>5. Optimal Intention Parameter</h2>

<div class="theorem">
    <strong>Theorem 5.1 (Optimal Worker Allocation).</strong>
    The value of $I_{rf}$ that minimizes $\mathbb{E}[T]$ is:
    $$
    \boxed{\; I_{rf}^\star = \frac{(1 - S_0)\, p_r}{(1 - S_0)\, p_r + p_f} \;}
    $$
</div>

<div class="proof">
    When the find and resolve phases overlap substantially, the completion time is dominated
    by the <em>slower</em> pipeline. The max-formulation gives:
    $$
    T \approx \max\!\left(\frac{(1-S_0)\, N}{I_{rf}\, m\, p_f},\;
    \frac{N}{(1-I_{rf})\, m\, p_r}\right).
    $$
    This is minimized when the two arguments are equal (balancing the bottlenecks):
    $$
    \frac{(1-S_0)}{I_{rf}\, p_f} = \frac{1}{(1-I_{rf})\, p_r}.
    $$
    Cross-multiplying:
    $$
    (1-S_0)\,(1-I_{rf})\, p_r = I_{rf}\, p_f.
    $$
    Expanding and collecting $I_{rf}$:
    $$
    (1-S_0)\, p_r - (1-S_0)\, p_r\, I_{rf} = I_{rf}\, p_f,
    $$
    $$
    (1-S_0)\, p_r = I_{rf}\,\big[(1-S_0)\, p_r + p_f\big],
    $$
    $$
    I_{rf}^\star = \frac{(1-S_0)\, p_r}{(1-S_0)\, p_r + p_f}.
    $$
    The minimum of a max of two functions — one decreasing, one increasing — is
    uniquely attained at their intersection.
    <div class="qed">$\blacksquare$</div>
</div>

<p>
    <strong>Interpretation:</strong> When there are many undiscovered issues (small $S_0$),
    more effort should go to finding ($I_{rf}^\star$ increases). When resolving is hard
    (small $p_r$), less effort should be diverted to finding. This is the
    <strong>load-balancing principle</strong>.
</p>

<p>
    Substituting $I_{rf}^\star$ back, the minimum expected completion time is:
</p>

$$
\mathbb{E}[T]_{\min} \approx \frac{N}{m} \left(\frac{1}{p_r} +
\frac{(1-S_0)}{p_f} + \frac{(1-S_0)}{p_r}\right)
= \frac{N}{m}\,\frac{(1-S_0) p_r + p_f + (1-S_0) p_f}{p_r\, p_f}.
$$

<p>Simplifying:</p>

$$
\boxed{\;\mathbb{E}[T]_{\min} \approx \frac{N}{m\, p_r\, p_f}
\Big[(1-S_0)(p_r + p_f) + p_f\Big]\;}
$$

<!-- ====================================================================== -->
<h2>6. Bottleneck Analysis</h2>

<h3>6.1 Phase 1 Delay</h3>

<p>
    Phase 1 (worker starvation) occurs when $A(0) = S_0 N < m$. In this regime,
    idle workers cannot resolve because there are too few known issues.
    From Proposition 3.1, the time to reach equilibrium $a^\star$ scales as:
</p>

$$
t_1 \approx \frac{1}{p_f + p_r} \ln\!\left(\frac{a^\star}{|a^\star - a_0|}\right)
= \frac{1}{p_f + p_r} \ln\!\left(\frac{m\, p_f}{|m\, p_f - (p_f + p_r)\, S_0 N|}\right).
$$

<div class="theorem">
    <strong>Proposition 6.1 (Bottleneck Threshold).</strong>
    Phase 1 is avoided entirely if and only if
    $$S_0 \ge \frac{m}{N}.$$
    Below this threshold, the expected delay penalty is $O\!\left(\frac{1}{p_f + p_r}
    \ln\frac{m}{S_0 N}\right)$.
</div>

<h3>6.2 The Last-Issue Effect</h3>

<p>
    Even after Phase 2, a "coupon-collector" effect appears in Phase 3: the last few issues
    take disproportionately long when $m > A(t)$. The expected time to resolve the final
    issue (when only 1 active issue remains and 1 worker resolves) is $1/p_r$,
    contributing an additive $O(1/p_r)$ tail.
</p>

<!-- ====================================================================== -->
<h2>7. Parameter Sensitivity</h2>

<table>
    <thead>
        <tr>
            <th>Parameter Change</th>
            <th>Effect on $\mathbb{E}[T]$</th>
            <th>Effect on $\text{Var}(T)$</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>$N \uparrow$</td>
            <td>Linear increase: $\mathbb{E}[T] = \Theta(N)$</td>
            <td>Linear increase; CV $\propto 1/\sqrt{N}$</td>
        </tr>
        <tr>
            <td>$S_0 \uparrow$</td>
            <td>Reduces discovery phase linearly</td>
            <td>Reduces variance from discovery</td>
        </tr>
        <tr>
            <td>$m \uparrow$</td>
            <td>$\propto 1/m$ when $S_0 N \ge m$; diminishing returns when bottlenecked</td>
            <td>$\propto 1/m^2$</td>
        </tr>
        <tr>
            <td>$p_r \uparrow$</td>
            <td>Reduces resolve phase; $\propto 1/p_r$</td>
            <td>Reduces resolve-phase variance</td>
        </tr>
        <tr>
            <td>$p_f \uparrow$</td>
            <td>Reduces find phase; $\propto 1/p_f$</td>
            <td>Reduces find-phase variance</td>
        </tr>
        <tr>
            <td>$I_{rf}$</td>
            <td><strong>U-shaped</strong>: minimum at $I_{rf}^\star$</td>
            <td>Minimum near $I_{rf}^\star$</td>
        </tr>
    </tbody>
</table>

<h3>7.1 Sensitivity Derivatives</h3>

<p>Taking partial derivatives of $\mu_T$:</p>

$$
\frac{\partial \mu_T}{\partial m} = -\frac{(1-S_0) N}{I_{rf}\, m^2\, p_f}
- \frac{N}{m^2\, p_r} < 0,
$$

$$
\frac{\partial \mu_T}{\partial p_r} = -\frac{N}{m\, p_r^2} < 0,
$$

$$
\frac{\partial \mu_T}{\partial I_{rf}} = -\frac{(1-S_0) N}{I_{rf}^2\, m\, p_f}
\quad \text{(from find term only; full derivative has positive resolve term)}.
$$

<h3>7.2 Sensitivity Charts</h3>

<p>
    The following charts illustrate how $\mathbb{E}[T]$ varies with each parameter individually,
    holding all others at their default values. Both the analytical approximation (solid line)
    and Monte Carlo estimates (dots with error bars showing $\pm 1\sigma$) are shown.
</p>

<div class="sens-defaults">
    Defaults: N=100, S₀=0.20, m=5, p<sub>r</sub>=0.30, p<sub>f</sub>=0.20, I<sub>rf</sub>=0.40
</div>

<div class="sensitivity-grid">
    <div class="sens-card">
        <h4>E[T] vs N (total issues)</h4>
        <div class="sens-chart-wrap"><canvas id="sens-N"></canvas></div>
    </div>
    <div class="sens-card">
        <h4>E[T] vs S₀ (initial discovery ratio)</h4>
        <div class="sens-chart-wrap"><canvas id="sens-S0"></canvas></div>
    </div>
    <div class="sens-card">
        <h4>E[T] vs m (number of workers)</h4>
        <div class="sens-chart-wrap"><canvas id="sens-m"></canvas></div>
    </div>
    <div class="sens-card">
        <h4>E[T] vs p<sub>r</sub> (resolve probability)</h4>
        <div class="sens-chart-wrap"><canvas id="sens-pr"></canvas></div>
    </div>
    <div class="sens-card">
        <h4>E[T] vs p<sub>f</sub> (find probability)</h4>
        <div class="sens-chart-wrap"><canvas id="sens-pf"></canvas></div>
    </div>
    <div class="sens-card">
        <h4>E[T] vs I<sub>rf</sub> (intention switch)</h4>
        <div class="sens-chart-wrap"><canvas id="sens-Irf"></canvas></div>
    </div>
</div>

<script>
(function() {
    // ── Shared simulation kernel ──
    function makeRng2(seed) {
        let s = [seed|0, (seed*1664525+1013904223)|0,
                 (seed*214013+2531011)|0, (seed*1103515245+12345)|0];
        function rotl(x,k) { return ((x<<k)|(x>>>(32-k)))>>>0; }
        return function() {
            const r = (rotl((s[1]*5)>>>0,7)*9)>>>0;
            const t = (s[1]<<9)>>>0;
            s[2]^=s[0]; s[3]^=s[1]; s[1]^=s[2]; s[0]^=s[3];
            s[2]^=t; s[3]=rotl(s[3],11);
            return (r>>>0)/4294967296;
        };
    }

    function simOne(N,S0,m,pr,pf,Irf,rng) {
        let K=Math.floor(S0*N), R=0, t=0;
        while(R<N && t<50000) {
            const A=K-R, U=N-K;
            let mFI = U>0 ? Math.round(Irf*m) : 0;
            let mRI = m - mFI, mR, mF;
            if(mRI>A){ mR=A; mF=U>0?Math.min(mFI+(mRI-A),m):0; }
            else { mR=mRI; mF=U>0?mFI:0; }
            let dF=0, dR=0;
            for(let i=0;i<mF;i++) if(rng()<pf) dF++;
            for(let i=0;i<mR;i++) if(rng()<pr) dR++;
            K+=Math.min(dF,U); R+=Math.min(dR,A); t++;
        }
        return t;
    }

    function mcStats(N,S0,m,pr,pf,Irf,nSim) {
        nSim = nSim || 600;
        const rng = makeRng2(12345);
        let sum=0, sum2=0;
        for(let i=0;i<nSim;i++) {
            const v = simOne(N,S0,m,pr,pf,Irf,rng);
            sum+=v; sum2+=v*v;
        }
        const mean = sum/nSim;
        const std = Math.sqrt(sum2/nSim - mean*mean);
        return {mean, std};
    }

    function analyticET(N,S0,m,pr,pf,Irf) {
        return (1-S0)*N/(Irf*m*pf) + N/(m*pr);
    }

    // ── Default parameters ──
    const D = {N:100, S0:0.20, m:5, pr:0.30, pf:0.20, Irf:0.40};

    // ── Chart builder ──
    function buildSensChart(canvasId, paramKey, values, labelFn) {
        const labels = values.map(v => labelFn ? labelFn(v) : v);
        const analytical = [];
        const mcMeans = [];
        const mcHi = [];
        const mcLo = [];

        for (const v of values) {
            const p = Object.assign({}, D);
            p[paramKey] = v;
            const aET = analyticET(p.N, p.S0, p.m, p.pr, p.pf, p.Irf);
            analytical.push(aET);
            const mc = mcStats(p.N, p.S0, p.m, p.pr, p.pf, p.Irf, 500);
            mcMeans.push(mc.mean);
            mcHi.push(mc.mean + mc.std);
            mcLo.push(mc.mean - mc.std);
        }

        const ctx = document.getElementById(canvasId).getContext('2d');
        new Chart(ctx, {
            type: 'line',
            data: {
                labels: labels,
                datasets: [
                    {
                        label: 'Analytical E[T]',
                        data: analytical,
                        borderColor: '#e67e22',
                        backgroundColor: 'rgba(230,126,34,0.1)',
                        borderWidth: 2.5,
                        pointRadius: 0,
                        tension: 0.3,
                        fill: false
                    },
                    {
                        label: 'MC E[T]',
                        data: mcMeans,
                        borderColor: '#2980b9',
                        backgroundColor: 'rgba(41,128,185,0.15)',
                        borderWidth: 2,
                        pointRadius: 3,
                        pointBackgroundColor: '#2980b9',
                        tension: 0.3,
                        fill: false
                    },
                    {
                        label: 'MC E[T]+σ',
                        data: mcHi,
                        borderColor: 'rgba(41,128,185,0.25)',
                        backgroundColor: 'rgba(41,128,185,0.08)',
                        borderWidth: 0,
                        pointRadius: 0,
                        fill: '+1',
                        tension: 0.3
                    },
                    {
                        label: 'MC E[T]−σ',
                        data: mcLo,
                        borderColor: 'rgba(41,128,185,0.25)',
                        backgroundColor: 'rgba(41,128,185,0.08)',
                        borderWidth: 0,
                        pointRadius: 0,
                        fill: '-1',
                        tension: 0.3
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                animation: { duration: 0 },
                scales: {
                    x: {
                        title: { display: true, text: paramKey === 'pr' ? 'p_r' : paramKey === 'pf' ? 'p_f' : paramKey === 'Irf' ? 'I_rf' : paramKey, font: {size:12} },
                        ticks: { maxTicksLimit: 12, font: {size:9} }
                    },
                    y: {
                        title: { display: true, text: 'E[T] (days)', font: {size:12} },
                        beginAtZero: false,
                        ticks: { font: {size:9} }
                    }
                },
                plugins: {
                    legend: {
                        display: true,
                        labels: { font: {size:9}, boxWidth: 14, filter: function(item) {
                            return item.text === 'Analytical E[T]' || item.text === 'MC E[T]';
                        }}
                    },
                    tooltip: {
                        filter: function(item) { return item.datasetIndex <= 1; },
                        callbacks: {
                            label: function(ctx) {
                                return ctx.dataset.label + ': ' + (+ctx.raw).toFixed(1);
                            }
                        }
                    }
                }
            }
        });
    }

    // ── Generate sweep values and build charts ──
    function range(a,b,step) {
        const r=[]; for(let v=a; v<=b+step*0.01; v+=step) r.push(Math.round(v*1000)/1000); return r;
    }
    function iRange(a,b,step) {
        const r=[]; for(let v=a; v<=b; v+=step) r.push(v); return r;
    }

    buildSensChart('sens-N',   'N',   iRange(20,400,20),   v=>v);
    buildSensChart('sens-S0',  'S0',  range(0.05,0.95,0.05), v=>v.toFixed(2));
    buildSensChart('sens-m',   'm',   iRange(1,30,1),      v=>v);
    buildSensChart('sens-pr',  'pr',  range(0.05,1.0,0.05), v=>v.toFixed(2));
    buildSensChart('sens-pf',  'pf',  range(0.05,1.0,0.05), v=>v.toFixed(2));
    buildSensChart('sens-Irf', 'Irf', range(0.05,0.95,0.05), v=>v.toFixed(2));
})();
</script>

<h3>7.3 Joint Sensitivity Surface: E[T] over (p<sub>r</sub>, p<sub>f</sub>)</h3>

<p>
    The 3D surface below visualizes how $\mathbb{E}[T]$ varies jointly with the resolve
    probability $p_r$ and the find probability $p_f$, while keeping all other parameters
    fixed. The surface is computed from the analytical formula. Drag to rotate, scroll to zoom.
</p>

<div class="surface-section">
    <h4>E[T] = f(p<sub>r</sub>, p<sub>f</sub>) &mdash; 3D Surface</h4>
    <div class="surface-params">
        <div class="param-row">
            <label>N =</label>
            <input type="range" id="s3-N" min="20" max="400" step="10" value="100">
            <span class="param-val" id="s3v-N">100</span>
        </div>
        <div class="param-row">
            <label>S₀ =</label>
            <input type="range" id="s3-S0" min="0.05" max="0.95" step="0.05" value="0.20">
            <span class="param-val" id="s3v-S0">0.20</span>
        </div>
        <div class="param-row">
            <label>m =</label>
            <input type="range" id="s3-m" min="1" max="30" step="1" value="5">
            <span class="param-val" id="s3v-m">5</span>
        </div>
        <div class="param-row">
            <label>I_rf =</label>
            <input type="range" id="s3-Irf" min="0.05" max="0.95" step="0.05" value="0.40">
            <span class="param-val" id="s3v-Irf">0.40</span>
        </div>
    </div>
    <div id="surface3d"></div>
</div>

<script>
(function() {
    function renderSurface() {
        const N   = +document.getElementById('s3-N').value;
        const S0  = +document.getElementById('s3-S0').value;
        const m   = +document.getElementById('s3-m').value;
        const Irf = +document.getElementById('s3-Irf').value;

        document.getElementById('s3v-N').textContent = N;
        document.getElementById('s3v-S0').textContent = S0.toFixed(2);
        document.getElementById('s3v-m').textContent = m;
        document.getElementById('s3v-Irf').textContent = Irf.toFixed(2);

        const nPts = 40;
        const prVals = [], pfVals = [];
        for (let i = 0; i < nPts; i++) {
            prVals.push(0.02 + i * 0.98 / (nPts - 1));
            pfVals.push(0.02 + i * 0.98 / (nPts - 1));
        }

        const z = [];
        for (let j = 0; j < nPts; j++) {
            const row = [];
            for (let i = 0; i < nPts; i++) {
                const pr = prVals[i];
                const pf = pfVals[j];
                const ET = (1 - S0) * N / (Irf * m * pf) + N / (m * pr);
                row.push(ET);
            }
            z.push(row);
        }

        const data = [{
            type: 'surface',
            x: prVals,
            y: pfVals,
            z: z,
            colorscale: [
                [0,    'rgb(  8, 48, 107)'],
                [0.15, 'rgb( 33,113, 181)'],
                [0.35, 'rgb( 66,146, 198)'],
                [0.55, 'rgb(161,217, 155)'],
                [0.75, 'rgb(253,174,  97)'],
                [0.9,  'rgb(215, 48,  39)'],
                [1,    'rgb(165,  0, 38)']
            ],
            colorbar: {
                title: { text: 'E[T] (days)', font: { size: 12 } },
                thickness: 18,
                len: 0.75
            },
            contours: {
                z: { show: true, usecolormap: true, highlightcolor: '#fff', project: { z: false } }
            },
            hovertemplate:
                'p<sub>r</sub> = %{x:.2f}<br>' +
                'p<sub>f</sub> = %{y:.2f}<br>' +
                'E[T] = %{z:.1f} days<extra></extra>'
        }];

        const layout = {
            scene: {
                xaxis: { title: { text: 'p_r (resolve prob.)' }, range: [0, 1] },
                yaxis: { title: { text: 'p_f (find prob.)' },    range: [0, 1] },
                zaxis: { title: { text: 'E[T] (days)' } },
                camera: { eye: { x: 1.6, y: -1.6, z: 0.9 } },
                aspectratio: { x: 1, y: 1, z: 0.7 }
            },
            margin: { l: 0, r: 0, t: 30, b: 0 },
            paper_bgcolor: 'rgba(0,0,0,0)',
            plot_bgcolor: 'rgba(0,0,0,0)',
            font: { family: 'Palatino Linotype, Georgia, serif', size: 11 }
        };

        const config = {
            responsive: true,
            displayModeBar: true,
            modeBarButtonsToRemove: ['toImage', 'sendDataToCloud'],
            displaylogo: false
        };

        Plotly.react('surface3d', data, layout, config);
    }

    // Debounced slider updates
    let sTimer = null;
    document.querySelectorAll('.surface-params input[type="range"]').forEach(sl => {
        sl.addEventListener('input', function() {
            const id = this.id.replace('s3-', 's3v-');
            const v = +this.value;
            document.getElementById(id).textContent =
                (this.step.includes('.') ? v.toFixed(2) : v.toString());
            clearTimeout(sTimer);
            sTimer = setTimeout(renderSurface, 200);
        });
    });

    // Initial render
    renderSurface();
})();
</script>

<!-- ====================================================================== -->
<h2>8. Numerical Validation</h2>

<p>
    We validate the analytical results via Monte Carlo simulation. The following Python code
    implements the Markov chain and estimates $\mathbb{E}[T]$ and $\text{Var}(T)$:
</p>

<pre><code>import numpy as np

def simulate_T(N, S0, m, p_r, p_f, I_rf, rng):
    K = int(S0 * N)
    R = 0
    t = 0
    while R < N:
        A = K - R
        U = N - K
        # Worker allocation
        m_find_intent = int(round(I_rf * m)) if U > 0 else 0
        m_resolve_intent = m - m_find_intent
        if m_resolve_intent > A:
            overflow = m_resolve_intent - A
            m_R = A
            m_F = min(m_find_intent + overflow, m) if U > 0 else 0
        else:
            m_R = m_resolve_intent
            m_F = m_find_intent if U > 0 else 0
        # Daily outcomes
        dF = rng.binomial(m_F, p_f) if m_F > 0 else 0
        dR = rng.binomial(m_R, p_r) if m_R > 0 else 0
        K += min(dF, U)
        R += min(dR, A)
        t += 1
    return t

# Parameters
N, S0, m, p_r, p_f, I_rf = 100, 0.2, 5, 0.3, 0.2, 0.4
rng = np.random.default_rng(42)
M = 10000

samples = [simulate_T(N, S0, m, p_r, p_f, I_rf, rng) for _ in range(M)]
print(f"E[T] = {np.mean(samples):.1f},  Std[T] = {np.std(samples):.1f}")

# Analytical prediction
mu_T = (1 - S0) * N / (I_rf * m * p_f) + N / (m * p_r)
print(f"Analytical E[T] ≈ {mu_T:.1f}")</code></pre>

<!-- ====================================================================== -->
<h2>9. Summary of Main Results</h2>

<div class="boxed">
    <div class="boxed-title">Summary.</div>
    <ol>
        <li>
            <strong>Model:</strong> The project is a 2-D absorbing Markov chain
            $(K(t), R(t))$ on $\{(K,R) : 0 \le R \le K \le N\}$ with absorbing state $(N,N)$.
        </li>
        <li>
            <strong>Mean completion time:</strong>
            $\displaystyle \mathbb{E}[T] \approx \frac{(1-S_0)N}{I_{rf}\, m\, p_f} + \frac{N}{m\, p_r}.$
        </li>
        <li>
            <strong>Asymptotic distribution:</strong> $T$ is approximately Gaussian for large $N$,
            with variance $\sigma_T^2 = O(N / m^2)$.
        </li>
        <li>
            <strong>Optimal allocation:</strong>
            $\displaystyle I_{rf}^\star = \frac{(1-S_0)\, p_r}{(1-S_0)\, p_r + p_f}.$
        </li>
        <li>
            <strong>Bottleneck:</strong> Worker starvation occurs when $S_0 < m/N$,
            adding logarithmic delay.
        </li>
    </ol>
</div>


</body>
</html>
