let grains = [];
let time = 0;
let isPaused = false;
let growthRateSlider;

function setup() {
  createCanvas(800, 600);
  
  // Создаем начальные зерна случайных размеров
  for (let i = 0; i < 50; i++) {
    grains.push(new Grain(
      random(width),
      random(height),
      random(10, 30),
      i
    ));
  }
  
  // Слайдер для контроля скорости роста
  growthRateSlider = createSlider(0, 0.1, 0.02, 0.001);
  growthRateSlider.position(20, height - 30);
  
  // Кнопка паузы
  let pauseButton = createButton('Пауза/Продолжить');
  pauseButton.position(150, height - 30);
  pauseButton.mousePressed(togglePause);
}

function draw() {
  background(240);
  
  // Обновляем и отображаем зерна
  if (!isPaused) {
    time += 0.01;
    updateGrains();
  }
  
  displayGrains();
  displayInfo();
}

function updateGrains() {
  // Сортируем зерна по размеру (крупные будут расти быстрее)
  grains.sort((a, b) => b.size - a.size);
  
  // Обновляем каждое зерно
  for (let i = 0; i < grains.length; i++) {
    grains[i].update();
    
    // Проверяем столкновения и поглощение мелких зерен
    for (let j = grains.length - 1; j > i; j--) {
      if (grains[i].checkCollision(grains[j])) {
        grains[i].absorb(grains[j]);
        grains.splice(j, 1);
      }
    }
  }
  
  // Иногда добавляем новые мелкие зерна
  if (random() < 0.02 && grains.length < 100) {
    grains.push(new Grain(
      random(width),
      random(height),
      random(5, 15),
      grains.length
    ));
  }
}

function displayGrains() {
  for (let grain of grains) {
    grain.display();
  }
}

function displayInfo() {
  fill(255, 230);
  rect(0, height - 60, width, 60);
  
  fill(0);
  textSize(16);
  text(`Количество зерен: ${grains.length}`, 20, height - 40);
  text(`Скорость роста: ${growthRateSlider.value().toFixed(3)}`, 20, height - 20);
  
  // Легенда
  textSize(12);
  text("Крупные зерна растут за счет поглощения мелких", width/2, height - 30);
}

function togglePause() {
  isPaused = !isPaused;
}

class Grain {
  constructor(x, y, size, id) {
    this.pos = createVector(x, y);
    this.size = size;
    this.baseSize = size;
    this.id = id;
    this.color = color(
      random(100, 200),
      random(100, 200),
      random(200, 255)
    );
    this.growthRate = random(0.8, 1.2);
    this.wobble = random(0, 100);
  }
  
  update() {
    // Крупные зерна растут быстрее (закон собирательной рекристаллизации)
    const growthFactor = map(this.size, 10, 100, 0.5, 2);
    this.size += growthRateSlider.value() * this.growthRate * growthFactor;
    
    // Небольшое случайное движение
    this.pos.x += random(-0.5, 0.5);
    this.pos.y += random(-0.5, 0.5);
    
    // Удерживаем зерна в пределах холста
    this.pos.x = constrain(this.pos.x, this.size/2, width - this.size/2);
    this.pos.y = constrain(this.pos.y, this.size/2, height - this.size/2);
  }
  
  display() {
    noStroke();
    fill(this.color);
    
    // Рисуем зерно с небольшими неровностями для реалистичности
    beginShape();
    for (let angle = 0; angle < TWO_PI; angle += PI/10) {
      let r = this.size/2 + noise(this.wobble + angle) * 5;
      let x = this.pos.x + r * cos(angle);
      let y = this.pos.y + r * sin(angle);
      vertex(x, y);
    }
    endShape(CLOSE);
    
    // Показываем ID для отладки (можно закомментировать)
    // fill(0);
    // textSize(10);
    // text(this.id, this.pos.x, this.pos.y);
  }
  
  checkCollision(other) {
    let d = dist(this.pos.x, this.pos.y, other.pos.x, other.pos.y);
    return d < (this.size/2 + other.size/2) * 0.8;
  }
  
  absorb(other) {
    // Увеличиваем размер пропорционально объему поглощенного зерна
    const thisVolume = PI * pow(this.size/2, 2);
    const otherVolume = PI * pow(other.size/2, 2);
    this.size = 2 * sqrt((thisVolume + otherVolume) / PI);
    
    // Смешиваем цвета
    this.color = lerpColor(this.color, other.color, 0.2);
  }
}
