<script lang="ts">
    import { techIconsPath } from '../constants/constants'
    export let title: string;
    export let tech: string[];

    type Technology = {
        name: string;
        imageSrc: string;
    };

    let technologies: Technology[] = tech.map((tech: string) => {
        return {
            name: tech,
            imageSrc: techIconsPath[tech.toLowerCase()]
        }
    });

</script>
<div class="window p-4 mt-5 bg-silver text-center">
    <span class="text-blue-800">Technologies used to build {title}</span>
    <!-- add preview elements -->
    <div class="flex flex-row justify-center">
        {#each technologies as tech}
            <div class="text-center full-win-icon text-blue-800 m-3">
                <div style="background-image: url('{tech.imageSrc}');" class="win-icon text-center" />
                <div class="window-element-title">{tech.name}</div>
            </div>
        {/each}
    </div>
</div>


<style>
    .window {
        position: relative;
        top: 0;
        left: 0;
        width: auto;
        margin-top: -168px;
        height: auto;
        max-width: 600px;
        max-height: 400px;
        background-color: silver;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
		border-left: 1px solid #fff;
		border-right: 1px solid gray;
		border-bottom: 1px solid gray;
		box-shadow: inset 1px 1px #dfdfdf, 1px 0 #000, 0 1px #000, 1px 1px #000;
    }
    .window-bar {
        top: 5px;
        position: absolute;
        margin-top: -4px;
        width: auto;
        padding-left: 4px;
        width: 100%;
        user-select: none;
        border-top: 1px solid #fff;
        border-left: 1px solid #fff;
        border-right: 1px solid gray;
		box-shadow: inset 1px 1px #dfdfdf, 1px 0 #000, 0 1px #ffffff00;
        z-index: 1; 
    }
    .window-bar-title{
        width: 100%;
        height: 24px;
        padding-top: .1em;
    }
    .win-icon {
		width: 64px;
		height: 64px;
		background-size: 64px 64px;
		background-repeat: no-repeat;
		background-position: center;
		margin: auto;
	}
    .close {
        position: absolute;
        right: 0.1em;
        top: .1em;
        width: 1.2em;
        height: 1.2em;
        opacity: 1;
        cursor: pointer;
        z-index: 1;
    }
    .close:hover {
        opacity: 1;
        border-top: 1px solid #fff;
        border-left: 1px solid #fff;
        border-right: 1px solid gray;
        border-bottom: 1px solid gray;
    }
    .close:active{
        background-color: silver;
        border-top: 1px solid #fff;
        border-left: 1px solid #fff;
        border-right: 1px solid gray;
        border-bottom: 1px solid gray;
    }
    .close:before, .close:after {
        position: absolute;
        left: 7px;
        top: 2px;
        content: ' ';
        height: 1em;
        width: 2px;
        background-color: rgb(236, 9, 9);
        
    }
    .close:before {
        transform: rotate(45deg);
    }
    .close:after {
        transform: rotate(-45deg);
    }
</style>