<template>
  <div ref="emblaRef" class="embla-picker__viewport">
    <div class="embla-picker__container">
      <div
        v-for="index in [...Array(60).keys()]"
        :key="index"
        class="embla-picker__slide"
      >
        {{ index }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import emblaCarouselVue from "embla-carousel-vue";
import { WheelGesturesPlugin } from "embla-carousel-wheel-gestures";
import type { EmblaCarouselType, EmblaOptionsType } from "embla-carousel";

const CIRCLE_DEGREES = 360;
const WHEEL_ITEM_SIZE = 30;
const WHEEL_ITEM_COUNT = 18;
const WHEEL_ITEMS_IN_VIEW = 4;

const WHEEL_ITEM_RADIUS = CIRCLE_DEGREES / WHEEL_ITEM_COUNT;
const IN_VIEW_DEGREES = WHEEL_ITEM_RADIUS * WHEEL_ITEMS_IN_VIEW;
const WHEEL_RADIUS = Math.round(
  WHEEL_ITEM_SIZE / 2 / Math.tan(Math.PI / WHEEL_ITEM_COUNT),
);

const options = ref<EmblaOptionsType>({
  dragFree: true,
  containScroll: false,
  loop: false,
  axis: "y",
  watchSlides: false,
});
const [emblaRef, emblaApi] = emblaCarouselVue(options, [WheelGesturesPlugin()]);

const slideIsInView = (wheelLocation: number, slidePosition: number): boolean =>
  Math.abs(wheelLocation - slidePosition) < IN_VIEW_DEGREES;

const rotateSlide = (
  emblaApi: EmblaCarouselType,
  index: number,
  loop: boolean,
  slideCount: number,
  totalRadius: number,
): void => {
  const slideNode = emblaApi.slideNodes()[index];
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
    slideNode.style.opacity = "1";
    slideNode.style.transform = `translateY(-${
      index * 100
    }%) rotateX(${angle}deg) translateZ(${WHEEL_RADIUS}px)`;
  } else {
    slideNode.style.opacity = "0";
    slideNode.style.transform = "none";
  }
};

const rotateSlides = (
  emblaApi: EmblaCarouselType,
  loop: boolean,
  slideCount: number,
  totalRadius: number,
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
  rotationOffset: number,
): (() => void) => {
  return (): void => {
    const rotation = slideCount * WHEEL_ITEM_RADIUS - rotationOffset;
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

onMounted(() => {
  if (!emblaApi.value) return;

  const slideCount = emblaApi.value.slideNodes().length;
  const {
    options: { loop },
  } = emblaApi.value.internalEngine();
  const totalRadius = slideCount * WHEEL_ITEM_RADIUS;
  const rotationOffset = loop ? 0 : WHEEL_ITEM_RADIUS;
  const rotateWheelFunc = rotateWheel(
    emblaApi.value,
    slideCount,
    rotationOffset,
  );
  const rotateSlidesFunc = rotateSlides(
    emblaApi.value,
    loop,
    slideCount,
    totalRadius,
  );
  const rotate = (): void => {
    rotateWheelFunc();
    rotateSlidesFunc();
  };

  emblaApi.value.on("pointerUp", () => {
    if (!emblaApi.value) return;
    const { scrollTo, target, location } = emblaApi.value.internalEngine();
    const diffToTarget = target.get() - location.get();
    const factor = Math.abs(diffToTarget) < WHEEL_ITEM_SIZE / 2.5 ? 10 : 0.1;
    const distance = diffToTarget * factor;
    scrollTo.distance(distance, true);
  });

  emblaApi.value.on("scroll", rotate);

  emblaApi.value.on("reInit", () => {
    if (!emblaApi.value) return;
    inactivateEmblaTransform(emblaApi.value);
    rotate();
  });

  inactivateEmblaTransform(emblaApi.value);
  rotate();
});
</script>

<style scoped>
.embla-picker__viewport {
  height: 32px;
  width: 100%;
  perspective: 1000px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -webkit-touch-callout: none;
  -khtml-user-select: none;
  -webkit-tap-highlight-color: transparent;
}
.embla-picker__container {
  height: 100%;
  width: 100%;
  transform-style: preserve-3d;
  will-change: transform;
}
.embla-picker__slide {
  width: 100%;
  height: 100%;
  font-size: 19px;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
  backface-visibility: hidden;
  opacity: 1;
}
</style>
