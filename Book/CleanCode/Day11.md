### π€ λ¦¬ν©ν λ§ μ 
```javascript
const merry = document.querySelector(".js-clock");

function getClock() {
const christmas = new Date("2021, 12, 25");
const date = new Date();
const timeGap = christmas - date;

const xDay = Math.floor(timeGap / (1000 * 60 * 60 * 24));
const xHours = Math.floor(
(timeGap - xDay * 1000 * 60 * 60 * 24) / (1000 * 60 * 60)
);
const xMinutes = Math.floor((timeGap % (60 * 60 * 1000)) / (60 * 1000));
const xSeconds = Math.floor((timeGap % (60 * 1000)) / 1000);

const day = String(xDay).padStart(2, "0");
const hours = String(xHours).padStart(2, "0");
const minutes = String(xMinutes).padStart(2, "0");
const seconds = String(xSeconds).padStart(2, "0");

merry.innerText = `${day}d ${hours}h ${minutes}m ${seconds}s`;
}

getClock();
setInterval(getClock, 1000);
```

### π λ¦¬ν©ν λ§ ν
```javascript
const merry = document.querySelector(".js-clock");

const SECONDS = 1000;
const MINUTES = SECONDS * 60;
const HOURS = MINUTES * 60;
const DAY = HOURS * 24;

function getClock() {
    const christmas = new Date("2021, 12, 25");
    const current = new Date();
    const gap = christmas - current;

    const originDay = Math.floor(gap / DAY);
    const originHours = Math.floor((gap - originDay * DAY) / HOURS);
    const originMinutes = Math.floor(gap % HOURS / MINUTES);
    const originSeconds = Math.floor(gap % MINUTES / SECONDS);

    const day = setFormat(originDay);
    const hours = setFormat(originHours);
    const minutes = setFormat(originMinutes);
    const seconds = setFormat(originSeconds);

    merry.innerText = `${day}d ${hours}h ${minutes}m ${seconds}s`;
}

function setFormat(day) {
    String(day).padStart(2, "0");
}

const clock = getClock();
setInterval(clock, 1000);
```

### π€ μκ°
μ±μμ λ³Έ λ΄μ©μ μ€μ λ‘ μ μ©νλ €κ³  νλ μκ°λ³΄λ€ μ΄λ €μ λ€π λ μλ°μ€ν¬λ¦½νΈλ₯Ό μν΄λ³Έμ§ λ~λ¬΄ μ€λλΌμ λ§μ΄ ν€λ§Έλ€. ~~μλ°μμΌλ©΄ μ’μμνλ°!~~ μ¬κΈ°μ λ ν¨μ¨μ μΌλ‘ λ§λ€ μ μμ κ² κ°μλ° κ·Έλ¬μ§ λͺ»ν΄μ μμ¬μμ΄ λ§μ΄ λ¨λλ€. μλ°μ€ν¬λ¦½νΈλ₯Ό λ€μ κ³΅λΆνκ³  νλ² λ λ¦¬ν©ν λ§μ λμ ν΄λ΄μΌκ² λ€β
