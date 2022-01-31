### 🤔 리팩토링 전
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

### 😊 리팩토링 후
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

### 🎤 소감
책에서 본 내용을 실제로 적용하려고 하니 생각보다 어려웠다😂 또 자바스크립트를 안해본지 너~무 오래돼서 많이 헤맸다. ~~자바였으면 좋았을텐데!~~ 여기서 더 효율적으로 만들 수 있을 것 같은데 그러지 못해서 아쉬움이 많이 남는다. 자바스크립트를 다시 공부하고 한번 더 리팩토링에 도전해봐야겠다✊
