import { useState, useEffect, useRef, type FC } from 'react';
import type { NextPage } from 'next';
import Head from 'next/head';
import Image from 'next/image'; // Importujemy komponent Image z Next.js
import { Video, Phone, MoreVertical, Smile, Paperclip, Mic, Send, Check, CheckCheck } from 'lucide-react';
import { v4 as uuidv4 } from 'uuid'; // Do generowania unikalnych ID

// Potrzebujesz zainstalować uuid:
// npm install uuid @types/uuid
// lub yarn add uuid @types/uuid

// --- TYPE DEFINITIONS ---
type MessageStatus = 'sent' | 'delivered' | 'read';

type Message = {
  id: string; // Zmieniamy na string dla uuid
  text: string;
  timestamp: string;
  sender: 'me' | 'them';
  status: MessageStatus;
};

// --- MOCK DATA ---
const initialMessages: Message[] = [
  {
    id: uuidv4(),
    text: "Hey, how's it going?",
    timestamp: '10:00 AM',
    sender: 'them',
    status: 'read',
  },
  {
    id: uuidv4(),
    text: "I'm good, thanks! Just working on a new project. You?",
    timestamp: '10:01 AM',
    sender: 'me',
    status: 'read',
  },
  {
    id: uuidv4(),
    text: 'Awesome! I am exploring some new design patterns. Found anything interesting?',
    timestamp: '10:02 AM',
    sender: 'them',
    status: 'read',
  },
  {
    id: uuidv4(),
    text: 'Definitely. I have been looking into state machines for complex UI logic. It is a game changer!',
    timestamp: '10:03 AM',
    sender: 'me',
    status: 'read',
  },
  {
    id: uuidv4(),
    text: 'Oh, that sounds cool! Send me some links when you have a moment.',
    timestamp: '10:04 AM',
    sender: 'them',
    status: 'read',
  },
];

// --- HELPER COMPONENTS ---
const MessageStatusIcon: FC<{ status: MessageStatus }> = ({ status }) => {
  if (status === 'read') {
    return <CheckCheck size={16} className="text-blue-500" aria-label="Wiadomość przeczytana" />;
  }
  if (status === 'delivered') {
    return <CheckCheck size={16} className="text-gray-500" aria-label="Wiadomość dostarczona" />;
  }
  return <Check size={16} className="text-gray-500" aria-label="Wiadomość wysłana" />;
};

const MessageBubble: FC<{ message: Message }> = ({ message }) => {
  const isMe = message.sender === 'me';
  return (
    <div className={`flex ${isMe ? 'justify-end' : 'justify-start'}`}>
      <div
        className={`max-w-sm rounded-lg px-3 py-2 shadow-sm ${isMe ? 'bg-[#DCF8C6]' : 'bg-white'}`}>
        <p className="text-sm text-gray-800">{message.text}</p>
        <div className="mt-1 flex items-center justify-end gap-2">
          <p className="text-xs text-gray-500">{message.timestamp}</p>
          {isMe && <MessageStatusIcon status={message.status} />}
        </div>
      </div>
    </div>
  );
};

// --- MAIN COMPONENT ---
const WhatsAppClonePage: NextPage = () => {
  const [messages, setMessages] = useState<Message[]>(initialMessages);
  const [newMessage, setNewMessage] = useState('');
  const messagesEndRef = useRef<HTMLDivElement>(null);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  const handleSendMessage = (e: React.FormEvent) => {
    e.preventDefault();
    if (newMessage.trim() === '') return;

    const userMessage: Message = {
      id: uuidv4(), // Używamy uuid do generowania unikalnego ID
      text: newMessage,
      timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
      sender: 'me',
      status: 'sent',
    };

    setMessages(prev => [...prev, userMessage]);
    setNewMessage('');

    // Symulacja aktualizacji statusu wiadomości wysłanej przez użytkownika:
    // Po 0.5s: "sent" -> "delivered"
    setTimeout(() => {
      setMessages(prevMsgs => prevMsgs.map(msg =>
        msg.id === userMessage.id ? { ...msg, status: 'delivered' } : msg
      ));
    }, 500);

    // Symulacja odpowiedzi od drugiej osoby i oznaczenia wiadomości użytkownika jako "read":
    setTimeout(() => {
      setMessages(prevMsgs => prevMsgs.map(msg =>
        msg.id === userMessage.id ? { ...msg, status: 'read' } : msg
      ));

      const replyMessage: Message = {
        id: uuidv4(), // Używamy uuid do generowania unikalnego ID
        text: 'Sounds interesting! Tell me more.',
        timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
        sender: 'them',
        status: 'read',
      };
      setMessages(prev => [...prev, replyMessage]);
    }, 2500); // Wydłużono czas, aby widoczne były etapy 'sent', 'delivered', 'read'
  };

  return (
    <>
      <Head>
        <title>WhatsApp Clone</title>
        <meta name="description" content="A UI clone of the WhatsApp chat interface" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className="flex h-screen w-full items-center justify-center bg-gray-200 font-sans">
        <div className="mx-auto flex h-full w-full max-w-lg flex-col border-x border-gray-300 bg-[#E5DDD5] md:h-[90vh] md:rounded-xl md:shadow-2xl">
          {/* Chat Header */}
          <header className="flex shrink-0 items-center justify-between bg-[#008069] px-3 py-2 text-white shadow-md">
            <div className="flex items-center gap-3">
              {/* Zastąpienie placeholderem z Image komponentem Next.js */}
              <div className="relative h-10 w-10">
                <Image
                  src="/avatar.jpg" // Musisz dodać ten plik do katalogu public/
                  alt="Avatar profilowy Jane Doe"
                  layout="fill"
                  objectFit="cover"
                  className="rounded-full border-2 border-white"
                />
              </div>
              <div>
                <h1 className="text-base font-medium">Jane Doe</h1>
                <p className="text-xs text-gray-200" style={{ color: 'rgba(255, 255, 255, 0.8)' }}>online</p> {/* Poprawiony kontrast */}
              </div>
            </div>
            <div className="flex items-center gap-4">
              <Video size={22} className="cursor-pointer" aria-label="Rozpocznij połączenie wideo" />
              <Phone size={20} className="cursor-pointer" aria-label="Rozpocznij połączenie głosowe" />
              <MoreVertical size={22} className="cursor-pointer" aria-label="Więcej opcji czatu" />
            </div>
          </header>

          {/* Messages Area */}
          <main className="flex-grow overflow-y-auto p-4">
            <div className="space-y-4">
              {messages.map((msg) => (
                <MessageBubble key={msg.id} message={msg} />
              ))}
              <div ref={messagesEndRef} />
            </div>
          </main>

          {/* Input Footer */}
          <footer className="shrink-0 bg-gray-100 px-3 py-2">
            <form onSubmit={handleSendMessage} className="flex items-center gap-2">
              <Smile size={24} className="cursor-pointer text-gray-500" aria-label="Wybierz emoji" />
              <Paperclip size={24} className="cursor-pointer text-gray-500" aria-label="Dołącz plik" />
              <input
                type="text"
                value={newMessage}
                onChange={(e) => setNewMessage(e.target.value)}
                placeholder="Type a message"
                aria-label="Wpisz wiadomość"
                className="w-full rounded-full border-none px-4 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#008069]/50"
              />
              <button
                type="submit"
                className="flex h-11 w-11 shrink-0 items-center justify-center rounded-full bg-[#008069] text-white"
                aria-label={newMessage ? "Wyślij wiadomość" : "Nagraj wiadomość głosową"}
              >
                {newMessage ? <Send size={20} /> : <Mic size={20} />}
              </button>
            </form>
          </footer>
        </div>
      </div>
    </>
  );
};

export default WhatsAppClonePage;
