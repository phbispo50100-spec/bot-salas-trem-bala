const { Client, GatewayIntentBits } = require('discord.js');

const client = new Client({
    intents: [
        GatewayIntentBits.Guilds,
        GatewayIntentBits.GuildVoiceStates
    ]
});

const TOKEN = process.env.DISCORD_TOKEN;
const CANAL_CRIADOR = 'COLOQUE_O_ID_DO_CANAL';

client.on('voiceStateUpdate', async (oldState, newState) => {
    if (newState.channelId !== CANAL_CRIADOR) return;

    const guild = newState.guild;
    const member = newState.member;

    try {
        const sala = await guild.channels.create({
            name: `Sala de ${member.user.username}`,
            type: 2,
            parent: newState.channel.parentId,
            permissionOverwrites: [
                {
                    id: member.id,
                    allow: ['ManageChannels', 'Connect', 'ViewChannel']
                }
            ]
        });

        await member.voice.setChannel(sala);
    } catch (error) {
        console.error('Erro ao criar sala:', error);
    }
});

client.login(TOKEN);
