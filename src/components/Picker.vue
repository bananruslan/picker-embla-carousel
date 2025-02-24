<script setup lang="ts">
import { ref, onMounted } from 'vue';
import emblaCarouselVue from 'embla-carousel-vue';

const CIRCLE_DEGREES = 360;
const WHEEL_ITEM_SIZE = 30;
const WHEEL_ITEM_COUNT = 18;
const WHEEL_ITEMS_IN_VIEW = 4;

const WHEEL_ITEM_RADIUS = CIRCLE_DEGREES / WHEEL_ITEM_COUNT;
const IN_VIEW_DEGREES = WHEEL_ITEM_RADIUS * WHEEL_ITEMS_IN_VIEW;
const WHEEL_RADIUS = Math.round(
  WHEEL_ITEM_SIZE / 2 / Math.tan(Math.PI / WHEEL_ITEM_COUNT)
);

const [emblaRef, emblaApi] = emblaCarouselVue({
  dragFree: true,
  containScroll: false,
  loop: false,
  axis: 'y',
  watchSlides: false,
});

onMounted(() => {
  const slideCount = emblaApi.value.slideNodes().length;
  const {
    options: { loop },
  } = emblaApi.value.internalEngine();
  const totalRadius = slideCount * WHEEL_ITEM_RADIUS;
  const rotationOffset = loop ? 0 : WHEEL_ITEM_RADIUS;
  const rotateWheelFunc = rotateWheel(
    emblaApi.value,
    slideCount,
    rotationOffset
  );
  const rotateSlidesFunc = rotateSlides(
    emblaApi.value,
    loop,
    slideCount,
    totalRadius
  );
  const rotate = (): void => {
    rotateWheelFunc();
    rotateSlidesFunc();
  };

  emblaApi.value.on('pointerUp', () => {
    const { scrollTo, target, location } = emblaApi.value.internalEngine();
    const diffToTarget = target.get() - location.get();
    const factor = Math.abs(diffToTarget) < WHEEL_ITEM_SIZE / 2.5 ? 10 : 0.1;
    const distance = diffToTarget * factor;
    scrollTo.distance(distance, true);
  });

  emblaApi.value.on('scroll', rotate);

  emblaApi.value.on('reInit', () => {
    inactivateEmblaTransform(emblaApi.value);
    rotate();
  });

  inactivateEmblaTransform(emblaApi.value);
  rotate();
});

const slideIsInView = (
  wheelLocation: number,
  slidePosition: number
): boolean => {
  return Math.abs(wheelLocation - slidePosition) < IN_VIEW_DEGREES;
};

const rotateSlide = (
  emblaApi: EmblaCarouselType,
  index: number,
  loop: boolean,
  slideCount: number,
  totalRadius: number
): void => {
  const slideNode = emblaApi.slideNodes()[index];
  console.log(emblaApi);
  const wheelLocation = emblaApi.scrollProgress() * totalRadius;
  const positionDefault = emblaApi.scrollSnapList()[index] * totalRadius;
  const positionLoopStart = positionDefault + totalRadius;
  const positionLoopEnd = positionDefault - totalRadius;

  let inView = false;
  let angle = index * -WHEEL_ITEM_RADIUS;

  if (slideIsInView(wheelLocation, positionDefault)) {
    inView = true;
  }

  if (loop && slideIsInView(wheelLocation, positionLoopEnd)) {
    inView = true;
    angle = -CIRCLE_DEGREES + (slideCount - index) * WHEEL_ITEM_RADIUS;
  }

  if (loop && slideIsInView(wheelLocation, positionLoopStart)) {
    inView = true;
    angle = -(totalRadius % CIRCLE_DEGREES) - index * WHEEL_ITEM_RADIUS;
  }

  if (inView) {
    slideNode.style.opacity = '1';
    slideNode.style.transform = `translateY(-${
      index * 100
    }%) rotateX(${angle}deg) translateZ(${WHEEL_RADIUS}px)`;
  } else {
    slideNode.style.opacity = '0';
    slideNode.style.transform = 'none';
  }
};

const rotateSlides = (
  emblaApi: EmblaCarouselType,
  loop: boolean,
  slideCount: number,
  totalRadius: number
): (() => void) => {
  return (): void => {
    for (let index = 0; index < slideCount; index += 1) {
      rotateSlide(emblaApi, index, loop, slideCount, totalRadius);
    }
  };
};

const rotateWheel = (
  emblaApi: EmblaCarouselType,
  slideCount: number,
  rotationOffset: number
): (() => void) => {
  return (): void => {
    const rotation = slideCount * WHEEL_ITEM_RADIUS - rotationOffset;
    console.log(emblaApi);
    const wheelRotation = rotation * emblaApi.scrollProgress();
    emblaApi.containerNode().style.transform = `translateZ(${WHEEL_RADIUS}px) rotateX(${wheelRotation}deg)`;
  };
};

const inactivateEmblaTransform = (emblaApi: EmblaCarouselType): void => {
  const { translate, slideLooper } = emblaApi.internalEngine();
  translate.clear();
  translate.toggleActive(false);
  slideLooper.loopPoints.forEach(({ translate }) => {
    translate.clear();
    translate.toggleActive(false);
  });
};
</script>

<template>
  <div ref="emblaRef" class="embla__ios-picker__container">
    <div class="embla__ios-picker__slide">0</div>
    <div class="embla__ios-picker__slide">1</div>
    <div class="embla__ios-picker__slide">2</div>
    <div class="embla__ios-picker__slide">3</div>
    <div class="embla__ios-picker__slide">4</div>
    <div class="embla__ios-picker__slide">5</div>
    <div class="embla__ios-picker__slide">6</div>
    <div class="embla__ios-picker__slide">7</div>
    <div class="embla__ios-picker__slide">8</div>
    <div class="embla__ios-picker__slide">9</div>
    <div class="embla__ios-picker__slide">10</div>
    <div class="embla__ios-picker__slide">11</div>
    <div class="embla__ios-picker__slide">12</div>
    <div class="embla__ios-picker__slide">13</div>
    <div class="embla__ios-picker__slide">14</div>
    <div class="embla__ios-picker__slide">15</div>
    <div class="embla__ios-picker__slide">16</div>
    <div class="embla__ios-picker__slide">17</div>
    <div class="embla__ios-picker__slide">18</div>
    <div class="embla__ios-picker__slide">19</div>
    <div class="embla__ios-picker__slide">20</div>
    <div class="embla__ios-picker__slide">21</div>
    <div class="embla__ios-picker__slide">22</div>
    <div class="embla__ios-picker__slide">23</div>
    <div class="embla__ios-picker__slide">24</div>
    <div class="embla__ios-picker__slide">25</div>
    <div class="embla__ios-picker__slide">26</div>
    <div class="embla__ios-picker__slide">27</div>
    <div class="embla__ios-picker__slide">28</div>
    <div class="embla__ios-picker__slide">29</div>
    <div class="embla__ios-picker__slide">30</div>
    <div class="embla__ios-picker__slide">31</div>
    <div class="embla__ios-picker__slide">32</div>
    <div class="embla__ios-picker__slide">33</div>
    <div class="embla__ios-picker__slide">34</div>
    <div class="embla__ios-picker__slide">35</div>
    <div class="embla__ios-picker__slide">36</div>
    <div class="embla__ios-picker__slide">37</div>
    <div class="embla__ios-picker__slide">38</div>
    <div class="embla__ios-picker__slide">39</div>
    <div class="embla__ios-picker__slide">40</div>
    <div class="embla__ios-picker__slide">41</div>
    <div class="embla__ios-picker__slide">42</div>
    <div class="embla__ios-picker__slide">43</div>
    <div class="embla__ios-picker__slide">44</div>
    <div class="embla__ios-picker__slide">45</div>
    <div class="embla__ios-picker__slide">46</div>
    <div class="embla__ios-picker__slide">47</div>
    <div class="embla__ios-picker__slide">48</div>
    <div class="embla__ios-picker__slide">49</div>
    <div class="embla__ios-picker__slide">50</div>
    <div class="embla__ios-picker__slide">51</div>
    <div class="embla__ios-picker__slide">52</div>
    <div class="embla__ios-picker__slide">53</div>
    <div class="embla__ios-picker__slide">54</div>
    <div class="embla__ios-picker__slide">55</div>
    <div class="embla__ios-picker__slide">56</div>
    <div class="embla__ios-picker__slide">57</div>
    <div class="embla__ios-picker__slide">58</div>
    <div class="embla__ios-picker__slide">59</div>
  </div>
</template>

<style scoped>
.embla {
  overflow: hidden;
}
.embla__container {
  display: flex;
}
.embla__slide {
  flex: 0 0 100%;
  min-width: 0;
}
</style>
