<script setup lang="ts">
import Fraction from 'fraction.js';
import { ref } from 'vue';
const xs = ref(2);
const inp = ref(Array.from({length: 3}, () => ""));
const ansnat = ref(""), anslat = ref("");
function addunit()
{
    ++ xs.value;
    if (inp.value.length <= xs.value) inp.value.push("");
}
function subunit()
{
    if (xs.value == 2) return window.alert("不能再减了");
    -- xs.value;
    // inp.value.pop();
}
function qpow(a : number, b : number)
{
    let res = 1;
    for (; b ; b /= 2, a *= a) if (b & 1) res *= a;
    return res;
}
function go()
{
    let n = xs.value;
    let a = Array.from({length: n + 1}, () => Array.from({length: n + 1}, () => new Fraction(0)));
    for (let i = 1; i <= n; ++ i)
    {
        for (let j = 1; j <= n; ++ j) (a[i] as Fraction[])[j] = new Fraction(qpow(i, j - 1));
        (a[i] as Fraction[])[n + 1] = new Fraction(inp.value[i] as string);
    }
    console.log(a);
    for (let i = 1; i <= n; ++ i)
    {
        let div = (a[i] as Fraction[])[i] as Fraction;
        for (let j = 1; j <= n + 1; ++ j)
            ((a[i] as Fraction[])[j] as Fraction) = (a[i] as Fraction[])[j]?.div(div) as Fraction;
        for (let j = 1; j <= n; ++ j)
        {
            if (j == i) continue;
            div = (a[j] as Fraction[])[i] as Fraction;
            for (let k = i; k <= n + 1; ++ k)
                ((a[j] as Fraction[])[k] as Fraction) = ((a[j] as Fraction[])[k] as Fraction).sub(((a[i] as Fraction[])[k] as Fraction).mul(div));
        }
    }
    console.log(a);
    ansnat.value = anslat.value = "";
    ansnat.value += "f(x)=";
    anslat.value += "$f(x)="
    let ok = 0;
    for (let i = n; i >= 1; -- i)
    {
        let t = (a[i] as Fraction[])[n + 1] as Fraction;
        if (t.valueOf() == 0) continue;
        if (ok && t.s > 0) ansnat.value += "+", anslat.value += "+";
        ok = 1;
        if (t.s < 0) ansnat.value += "-", anslat.value += "-";
        if (t.d == BigInt(1))
        {
            ansnat.value += t.n;
            anslat.value += t.n;
        }
        else
        {
            ansnat.value += "(" + t.n + "/" + t.d + ")";
            anslat.value += "\\frac{" + t.n + "}{" + t.d + "}"
        }
        if (i == 1) continue;
        else if (i == 2)
        {
            ansnat.value += "x";
            anslat.value += "x";
        }
        else
        {
            ansnat.value += "x^" + (i - 1);
            anslat.value += "x^{" + (i - 1) + "}";
        }
    }
    anslat.value += "$";
    (document.getElementById("answer") as HTMLElement).style.display = "block";
}
</script>

<template>
<h1>找规律暴力生成器</h1>
<div class="buttons">
    <button @click="addunit();">+</button>&nbsp;&nbsp;&nbsp;
    <button @click="subunit();">-</button>
</div>
<div style="height: 30px;">&emsp;</div>
<div class="inputs">
    <div v-for="i in xs">第 {{ i }} 个数：<input v-model="inp[i]" /><div style="height: 10px;">&emsp;</div></div>
</div>
<div style="height: 20px;">&emsp;</div>
<button @click="go();">OK</button>
<div id="answer" style="display: none;">
结果： {{ ansnat }}<br>
LaTeX： {{ anslat }}
</div>
</template>

<style scoped>
button
{
    font-size: 1.2em;
}
input
{
    font-size: 1.2em;
    width: 400px;
}
</style>
