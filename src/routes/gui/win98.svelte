<script lang="ts">
	import { browser } from '$app/env';
	import Projects from '../../components/Projects.svelte';
	import DraggableIcon from '../../components/DraggableIcon.svelte';
	import Terminal from '../../components/Terminal.svelte';
	import Window from '../../components/Window.svelte';
	import { windowPositionLocalStorage } from '../../store/stores';

	windowPositionLocalStorage.subscribe(val => {
		if(browser && val) return (localStorage.windowsPos = val);
	});

	let date: Date = new Date();
	let hours: number = date.getHours();
	let minutes: number = date.getMinutes();

	let time: string = `${hours}:${minutes}`;
	let dateString: string = `${date.getMonth()}/${date.getDate()}/${date.getFullYear()}`;
	
	let showStartMenu: boolean = false;
	let displayWindowComponent: boolean = false; //its in use
	let doubleClicked: boolean = false;
	let doubleClickTimer: number; //its in use

	// desktop icons
	type desktopIcon = {
		name: string;
		icon: string;
		left: string;
		top: string;
		onClick?: () => void;
		component: any;
	};

	let desktopIcons: desktopIcon[] = [
		{
			name: 'cmd',
			icon: '../win98/terminal.png',
			left: '50px',
			top: '50px',
			onClick: () => onIconClick('cmd', Terminal),
			component: Terminal
		},
		{
			name: 'projects',
			icon: '../win98/directory.png',
			left: '50px',
			top: '150px',
			onClick: () => onIconClick('projects', Projects),
			component: Projects
		}
	];

	// startmenu
	type startMenuProgram = {
		name: string;
		onClick?: () => void;
	};
	let startMenuPrograms: startMenuProgram[] = [
		{
			name: 'Portfolio'
		},
		{
			name: 'Resume'
		},
		{
			name: 'Contact'
		},
		{
			name: 'Terminal',
			onClick: () => onStartMenuItemClick('cmd', Terminal),
		}
	];

	// windows
	type SystemWindow = {
		name: string;
		component: any;
		left: number;
		top: number;
	};
	let openWindows: SystemWindow[] = [];


	// functions
	function getWinLeftPostition(name: string): number {
		if(localStorage.windowsPos){
			let local = JSON.parse(localStorage.windowsPos);
			let windowPos = local.find((window: string) => window.split(',')[0] === name);
			if (windowPos) {
				return parseInt(windowPos.split(',')[1]);
			}
		
		}
		return 10;
	}

	function getWinTopPostition(name: string): number {
		if(localStorage.windowsPos){
			let local = JSON.parse(localStorage.windowsPos);
			let windowPos = local.find((window: string) => window.split(',')[0] === name);
			if (windowPos) {
				return parseInt(windowPos.split(',')[2]);
			}
		}
		return 10;
	}

	function onStartClick() {
		showStartMenu = !showStartMenu;
	};

	function onStartMenuItemClick(name: string, component: any) {
		showStartMenu = false;
		if(!openWindows.find(w => w.name === name)) 
			openWindows = [...openWindows, { name: name, component: component, 
					left: getWinLeftPostition(name), top: getWinTopPostition(name) }];
	}

	function onStartMenuBarClick(name: string) {
		showStartMenu = false;
		if(browser){
			// get all elements with class
			let elements: Element[] = Array.from(document.getElementsByClassName(name+"element"));

			// trigger click on elements with that name
			elements.forEach(e => {
				let element = e as HTMLElement;
				element.click();
			});
		}
	}

	function onIconClick(name: string, component: any) {
		showStartMenu = false;
		doubleClickTimer = window.setTimeout(() => doubleClicked = false, 500);
		if (doubleClicked) {
			if(!openWindows.find(w => w.name === name)) 
				openWindows = [...openWindows, { name: name, component: component, 
					left: getWinLeftPostition(name), top: getWinTopPostition(name) }];
		}
		else  doubleClicked = true;
	}

	function onWindowClose(windowName: string) {
		// set left and top before removing window from array
		let window = openWindows.find(w => w.name === windowName);
		if (window) {
			window.left = getWinLeftPostition(windowName);
			window.top = getWinTopPostition(windowName);
		}
		
		// remove window from array
		openWindows = openWindows.filter(window => window.name !== windowName);
	}

	if (browser) {
		// close window on escape key press
		window.addEventListener('keydown', (e) => {
			if (e.keyCode === 27) displayWindowComponent = false;
		});

		// close window on close button click
		window.addEventListener('click', (e) => {
			if ((e.target as Element).classList.contains('close')) displayWindowComponent = false;
		});
	}
</script>

<svelte:head><title>Win98</title></svelte:head>
<div class="win98-bg">
	{#each desktopIcons as icon}
		<DraggableIcon left={icon.left} top={icon.top}>
			<div on:click={icon.onClick} class="text-center full-win-icon">
				<div style="background-image: url('{icon.icon}');" class="win-icon text-center" />
				<p class="text-center drop-shadow-lg ">{icon.name}</p>
			</div>
		</DraggableIcon>
	{/each}
	{#each openWindows as windw, index}
			<Window left={windw.left ?? 100} top={windw.top ?? 100}
				zIndex={index} title={windw.name} onClose={() => onWindowClose(windw.name)}>
				<svelte:component this={windw.component} />
			</Window>
	{/each}

	<!-- windows start menu -->
	<footer class="absolute inset-x-0 bottom-0">
		<!-- show on start click -->
		{#if showStartMenu}
			<div class="container start-menu bg-gray-200 border-t-2 border-gray-700 py-4">
				{#each startMenuPrograms as program}
					<button
						type="button"
						on:click={program.onClick}
						class="w-full inline-block px-6 py-2.5 text-gray-700 text-left font-bold text-xs leading-tight uppercase hover:bg-gray-300 focus:bg-gray-300 focus:ring-0 active:bg-gray-400 transition duration-150 ease-in-out"
						>{program.name}</button
					>
				{/each}
			</div>
		{/if}

		<div class="p-1 bg-gray-400 left-0 border-t-2 border-gray-700">
			<button
				type="button"
				on:click={onStartClick}
				class=" start-menu-button inset-y-0 px-6 py-3 bg-gray-200 text-gray-700 font-bold text-xs leading-tight uppercase hover:bg-gray-300 focus:bg-gray-300 focus:ring-0 active:bg-gray-400 transition duration-150 ease-in-out"
				>Start</button
			>
			<span class="fixed transparent-start-menu my-1.5 mx-2 font-bold border-l-2 px-2 border-gray-700">
				<div class="text-gray-700 text-xs text-center text-transparent"> a</div>
				<div class="text-gray-700 text-xs text-right text-transparent">a</div>
			</span>
			<span class="ml-3">
				{#each openWindows as windw, index}
					
					<button
						type="button"
						on:click={() => onStartMenuBarClick(windw.name)}
						class=" start-menu-button mr-1 inset-y-0 px-6 py-2 bg-gray-200 text-gray-700 font-bold text-xs leading-tight uppercase hover:bg-gray-300 focus:bg-gray-300 focus:ring-0 active:bg-gray-400 transition duration-150 ease-in-out"
						>{windw.name}</button
					>
				{/each}
			</span>
			
			<!-- show time and date -->
			<span class="fixed right-0 my-1.5 mx-2 font-bold border-l-2 px-2 border-gray-700">
				<div class="text-gray-700 text-xs text-center">{time}</div>
				<div class="text-gray-700 text-xs text-right">{dateString}</div>
			</span>
		</div>
	</footer>
</div>

<style>
	.transparent-start-menu {
		user-select: none;
		cursor: auto;
		left: 92px;
	}
	.win98-bg {
		background-color: #378872;
		color: #fff;
		padding: 0;
		height: 100vh;
		width: 100vw;
		font-family: 'Courier New', Courier, monospace;
		font-size: 1rem;
		max-height: 100vh;
	}
	.start-menu {
		max-width: 400px;
		background-color: silver;
	}

	.start-menu-button {
		background-color: silver;
		border-top: 1px solid #fff;
		border-left: 1px solid #fff;
		border-right: 1px solid gray;
		border-bottom: 1px solid gray;
		box-shadow: inset 1px 1px #dfdfdf, 1px 0 #000, 0 1px #000, 1px 1px #000;
	}

	.start-menu-button:active {
		background-color: #fff;
		box-shadow: inset 1px 1px #dfdfdf, 1px 0 #000, 0 1px #000, 1px 1px #000;
	}

	.win-icon {
		width: 64px;
		height: 64px;
		background-size: 64px 64px;
		background-repeat: no-repeat;
		background-position: center;
		margin: auto;
	}

	.full-win-icon {
		max-width: 100px;
		max-height: 100px;
		width: 100px;
		height: 100px;
		text-overflow: ellipsis;
		overflow: hidden;
	}
</style>
