<script lang="ts">
	import Peer from 'peerjs';
	import Message from './Message.svelte';
	import NewChatModal from './NewChatModal.svelte';

	const peer = new Peer();
	
	let otherPeerId = '';
	let connected = false;
	let connection: Peer.DataConnection;
	let myId = 'Getting ID ...';
	
	peer.on('open', function(id) {
		myId = id;
	});

	peer.on('connection', (conn) => {
		connection = conn;
		connection.on('open', () => {
			connected = true;
			otherPeerId = conn.peer;
		});
		connection.on('data', (data) => {
			messages = [...messages, data];
		});
		connection.on('close', () => {
			connected = false;
			otherPeerId = '';
			peer.destroy();
		});
	});

	function connect(peerId: string){
		connection = peer.connect(peerId);
		connection.on('open', () => {
			connected = true;
			otherPeerId = peerId;
		});
		connection.on('data', (data) => {
			messages = [...messages, data];
		});
		connection.on('close', () => {
			connected = false;
			otherPeerId = '';
			peer.destroy();
		});
	}

	enum MessageDirection {
		RECEIVED,
		SENT,
		SYSTEM
	}

	type Message = {
		text: string,
		time: Date,
		direction: MessageDirection
	}

	let messages: Array<Message> = [];
	export let messageText = '';

	function sendMessage(text: string){
		const message: Message = {
			direction: undefined,
			time: new Date(),
			text: text,
		}
		messages = [...messages, {...message, direction: MessageDirection.SENT}];
		connection.send({...message, direction: MessageDirection.RECEIVED});
	}

	function sendMessageHandler() {
		if (messageText) {
			sendMessage(messageText)
			messageText = '';
		}
	}

	function closeConnectionHandler() {
		connection.close();
		connected = false;
		otherPeerId = undefined;
	}

	function startConnectionHandler(event) {
		connect(event.detail);
	}
</script>

<main>
	<!--
		
	-->
	{#if !connected}
		<NewChatModal myPeerId={myId} on:connect={startConnectionHandler}></NewChatModal>
	{/if}
	<nav>
		<span class="material-icons">person</span>
		<div>
			<h1>Active Chat</h1>
			<small>{otherPeerId}</small>
		</div>
		<button on:click={closeConnectionHandler} class="no-style" disabled={!connected}>
			<span class="material-icons">close</span>
		</button>
	</nav>
	<div class="messages">
		{#each messages as message}
			<Message
				text={message.text}
				time={message.time}
				received={message.direction === MessageDirection.RECEIVED}
				sent={message.direction === MessageDirection.SENT}
			></Message>
		{/each}
	</div>
	<form class="chat-bar" on:submit|preventDefault={sendMessageHandler}>
		<input type="text" class="chatbox" placeholder="Message" bind:value={messageText} disabled={!connected}>
		<button on:click={sendMessageHandler} disabled={!connected} type="submit"><span class="material-icons">send</span></button>
	</form>
</main>

<style lang="scss">
main {
	height: 100%;
	display: grid;
  grid-template-columns: 1fr; 
  grid-template-rows: 3.5em 1fr 3.5em;
}

nav {
	background-color: var(--bg-secondary-color);
	overflow: hidden;
	display: flex;
	align-items: center;
	padding: 1rem;
	gap: 1rem;
	h1 {
		font-size: 1rem;
		font-weight: bold;
	}
	div {
		display: flex;
		flex-direction: column;
		flex-grow: 1;
	}
}

.messages {
	display: flex;
	flex-direction: column;
	gap: .3em;
	padding: 1em;
}

.chat-bar {
	display: flex;
	align-items: center;
	background-color: rgb(39, 39, 39);
	padding: .3rem;

	& > * {
		height: 100%;
	}

	button {
		background: none;
		border: none;
		color: #eee;
	}

	input {
		width: 100%;
		background: none;
		border: none;
		color: #eee;
	}

}
</style>