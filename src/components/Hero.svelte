<script lang="ts">
import { onMount } from "svelte";
import { siteConfig } from "../config";

export let bannerSrc = "";
export let bannerPosition = "center";

let scrollY = 0;
let innerHeight = 0;
let innerWidth = 0;
let canvas: HTMLCanvasElement;

// Configuration
const heroConfig = siteConfig?.hero || {
	name: "Blog",
	nameHeight: "100",
	subtitle: "",
};
const { name, nameHeight, subtitle } = heroConfig;

// State
let opacity = 1;
let contentOpacity = 1;
let scale = 1;
let translateY = 0;
let displayedName = "";
let isTyping = false;

// Computed styles
$: opacity = innerHeight ? Math.max(0, 1 - scrollY / (innerHeight * 0.8)) : 1;
$: scale = innerHeight ? Math.max(1, 1 + scrollY / (innerHeight * 5)) : 1;
$: translateY = -scrollY * 0.3;
$: contentOpacity = innerHeight
	? Math.max(0, 1 - scrollY / (innerHeight * 0.4))
	: 1;

// Typewriter Effect
const typeSpeed = 100;
let typeIndex = 0;

function startTyping() {
	if (isTyping || !name) return;
	displayedName = "";
	typeIndex = 0;
	isTyping = true;
	typeNextChar();
}

function typeNextChar() {
	if (!isTyping) return;
	if (typeIndex < name.length) {
		displayedName += name.charAt(typeIndex);
		typeIndex++;
		setTimeout(typeNextChar, typeSpeed);
	} else {
		isTyping = false;
	}
}

// Star Effect
class Star {
	x: number;
	y: number;
	size: number;
	speed: number;
	opacity: number;

	constructor(w: number, h: number) {
		this.x = Math.random() * w;
		this.y = Math.random() * h;
		this.size = Math.random() * 2 + 0.5;
		this.speed = Math.random() * 2 + 1;
		this.opacity = Math.random() * 0.7 + 0.3;
	}

	update(h: number, w: number) {
		this.y += this.speed;
		if (this.y > h) {
			this.y = 0;
			this.x = Math.random() * w;
		}
	}

	draw(context: CanvasRenderingContext2D) {
		context.fillStyle = `rgba(255, 255, 255, ${this.opacity})`;
		context.beginPath();
		context.arc(this.x, this.y, this.size, 0, Math.PI * 2);
		context.fill();
	}
}

let stars: Star[] = [];
let ctx: CanvasRenderingContext2D | null = null;
let animationFrame: number;

function initStars() {
	if (!canvas) return;
	ctx = canvas.getContext("2d");
	if (!ctx) return;

	canvas.width = window.innerWidth;
	canvas.height = window.innerHeight;

	stars = [];
	const starCount = window.innerWidth < 768 ? 50 : 150;
	for (let i = 0; i < starCount; i++) {
		stars.push(new Star(canvas.width, canvas.height));
	}

	animateStars();
}

function animateStars() {
	if (!ctx || !canvas) return;
	ctx.clearRect(0, 0, canvas.width, canvas.height);

	stars.forEach((star) => {
		star.update(canvas.height, canvas.width);
		star.draw(ctx!);
	});

	animationFrame = requestAnimationFrame(animateStars);
}

onMount(() => {
	innerHeight = window.innerHeight;
	innerWidth = window.innerWidth;

	// Ensure typewriter starts
	startTyping();
	initStars();

	const handleResize = () => {
		innerHeight = window.innerHeight;
		innerWidth = window.innerWidth;
		if (canvas) {
			canvas.width = innerWidth;
			canvas.height = innerHeight;
			stars = [];
			const starCount = innerWidth < 768 ? 80 : 200;
			for (let i = 0; i < starCount; i++) {
				stars.push(new Star(canvas.width, canvas.height));
			}
			// Explicitly redraw after resize
			animateStars();
		}
	};

	const handleScroll = () => {
		scrollY = window.scrollY;
	};

	window.addEventListener("resize", handleResize);
	window.addEventListener("scroll", handleScroll);

	// Initial trigger
	if (typeof window !== "undefined") {
		handleResize();
	}

	return () => {
		window.removeEventListener("resize", handleResize);
		window.removeEventListener("scroll", handleScroll);
		cancelAnimationFrame(animationFrame);
		isTyping = false; // Stop typewriter loop
	};
});

// Re-trigger typing when scrollY goes back to near 0
let wasNotAtTop = false;
$: {
	if (scrollY > 100) {
		wasNotAtTop = true;
	}
	if (scrollY < 10 && wasNotAtTop) {
		wasNotAtTop = false;
		startTyping();
	}
}
</script>

<div 
  class="hero-wrapper fixed top-0 left-0 w-full h-screen overflow-hidden z-[1]" 
  style="opacity: {opacity}; pointer-events: {opacity > 0.1 ? 'auto' : 'none'};"
>
  <!-- Background Image (z-0) -->
  <div 
    class="absolute inset-0 flex items-center justify-center bg-gray-900 z-0"
    style="transform: scale({scale}) translateY({translateY}px);"
  >
    {#if bannerSrc}
      <img 
        src={bannerSrc} 
        alt="Hero Background" 
        class="w-full h-full object-cover"
        style="object-position: {bannerPosition};"
      />
    {/if}
  </div>
  
  <!-- Overlay (z-10) -->
  <div class="absolute inset-0 bg-black/40 z-10 pointer-events-none"></div>

  <!-- Stars Canvas (z-20) -->
  <canvas 
    bind:this={canvas} 
    class="absolute inset-0 pointer-events-none z-20"
    style="transform: translateY({translateY * 0.8}px);"
  ></canvas>

  <!-- Content (z-30) -->
  <div 
    class="absolute inset-0 flex flex-col items-center justify-center text-center pointer-events-none z-30"
    style="opacity: {contentOpacity}; transform: translateY({translateY * 0.5}px);"
  >
    <h1 class="font-bold text-white mb-6 artistic-text" style="font-size: {parseInt(nameHeight) / 16}rem;">
      {displayedName}<span class="cursor">|</span>
    </h1>
    {#if subtitle}
      <p class="text-2xl text-white/90 font-light tracking-widest fade-in-up">
        {subtitle}
      </p>
    {/if}
  </div>
</div>

<style>
  /* Artistic Text + Shimmer */
  .artistic-text {
    font-family: 'JetBrains Mono', 'Microsoft YaHei', sans-serif;
    background: linear-gradient(
      to right, 
      #ffffff 0%, 
      #ffffff 40%, 
      #7dd3fc 50%, 
      #ffffff 60%, 
      #ffffff 100%
    );
    background-size: 200% auto;
    color: #ffffff;
    background-clip: text;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: shimmer 4s linear infinite;
    text-shadow: 0 0 30px rgba(125, 211, 252, 0.6);
    letter-spacing: 0.15em;
    filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.3));
    transition: all 0.3s ease;
  }

  @keyframes shimmer {
    0% { background-position: -100% center; }
    100% { background-position: 100% center; }
  }

  .cursor {
    display: inline-block;
    width: 2px;
    height: 1.2em;
    background-color: #7dd3fc;
    margin-left: 4px;
    vertical-align: middle;
    animation: blink 1s step-end infinite;
    box-shadow: 0 0 10px #7dd3fc;
  }

  @keyframes blink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0; }
  }
  
  .fade-in-up {
    animation: fadeInUp 1s ease-out forwards;
    opacity: 0;
    transform: translateY(20px);
    animation-delay: 0.5s;
  }
  
  @keyframes fadeInUp {
    to {
      opacity: 0.9;
      transform: translateY(0);
    }
  }
  
  /* Mobile adjustments */
  @media (max-width: 768px) {
    .artistic-text {
      font-size: 3rem !important; /* Override inline style for mobile */
    }
  }
</style>
