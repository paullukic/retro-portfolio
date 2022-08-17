<script lang="ts">
    export let submitForm: () => void;
</script> 
<form on:submit|preventDefault={submitForm}>
    <button type="submit">Submit</button>
</form>

<style>
    
</style>