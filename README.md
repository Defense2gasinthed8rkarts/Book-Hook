import { type CSSProperties, type ReactNode, createContext, useContext, useEffect, useMemo, useState } from 'react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ErrorBoundary } from '@/components/error-boundary';
import { Toaster } from '@/components/ui/toaster';
import { TooltipProvider } from '@/components/ui/tooltip';
import NotFound from '@/pages/not-found';
import {
  ArrowLeft,
  ArrowRight,
  BookOpen,
  Check,
  ChevronRight,
  CircleHelp,
 Clock3,
  Crown,
  ExternalLink,
  Flame,
  Heart,
  Map,
  Pause,
  PenLine,
  Plus,
  RotateCcw,
  Sparkles,
  Telescope,
} from 'lucide-react';
import { Link, Route, Switch, Router as WouterRouter, useLocation, useParams } from 'wouter';

type MoodId = 'adventure' | 'comfort' | 'weird' | 'mystery';
type Session = { mood: MoodId | null; savedStory: string | null; savedStories: string[]; reflection: string; reflectionEmojis: string[] };

type Story = {
  id: string;
  title: string;
  author: string;
  bookTitle: string;
 
  cover: string;
  character: string;
  theme: string;
  hook: string;
  synopsis: string;
  excerpt: string;
  sampleLabel?: string;
  effort: number;
  length: number;
  color: string;
  ink: string;
  icon: 'map' | 'telescope' | 'heart' | 'crown' | 'flame' | 'circle-help'| 'compass';
  mood: MoodId[];
  storyLink:string;
  callNumber: string;
  sublocation: string;
};

const stories: Story[] = [
  {
    id: 'do-over',
    title: 'Restart',
    bookTitle: '(A Do-over in real life)',
    author: 'Gordon Korman',
    cover: '/covers/restart.png',
    character: 'Chase',
    theme: 'second chances',
    hook: 'Can Chase start over without his memories?',
    synopsis: 'After falling from a roof, thirteen-year-old Chase Ambrose wakes up with no memory of who he used to be. At school, some students welcome back the star football player, while others flinch when he walks by—and Chase slowly discovers that he was the bully who made their lives miserable. His old friends expect him to return to his cruel ways, but the kids he hurt are watching to see whether he has really changed. Every hallway, conversation, and old friendship becomes a clue to the person Chase used to be. With no past to guide him, Chase must decide whether a person can truly start over—or whether everyone else will always remember the version of him he forgot.',
    excerpt: `“Where am I?” I demand. “Why am I in a hospital? Who are these people?”

The lady with the glasses gasps.

“Chase, honey,” she says in a nervous voice. “It’s me. Mom.”

Mom. Doesn’t she think I know my own mother?

“I’ve never seen you before in my life,” I bluster.

“My mother is... my mother is...”

That’s when it happens. I reach back for an image of Mom and come up totally empty. Ditto Dad, home, friends, school, or anything. It’s the craziest feeling. I remember how to remember, but when I actually try to do it, I’m a blank. I’m a computer with its hard drive wiped clean.`,

    effort: 38,
    length: 42,
    color: '#e9a04a',
    ink: '#49304d',
    icon: 'heart',
    mood: ['comfort', 'adventure'],
    storyLink:'https://read.amazon.com/sample/1338053809?clientId=share&asin=1338053809&revisionId=&format=4&depth=1',
    callNumber: 'F Kor',
    sublocation: 'Humor'
  },
  {
    id: 'aliens',
    title: 'Scouts',
    author: 'Shannon Greenland',
    bookTitle: '(Possible Chance of Aliens)', 
    cover: '/covers/scouts.jpg',
    character: 'a camping trip',
    theme: 'secret maps',
    hook: 'A strange map. One dangerous night.',
    synopsis: 'A group of best friends sneaks out for a camping trip to watch a once-in-a-lifetime meteor shower. Then something blazing crashes into the woods, and their fun night turns into a search for answers. A mysterious map, dangerous strangers, and the possibility that the object did not come from Earth pull the friends into a race through the dark. As the night grows stranger, every new clue makes the friends wonder who—or what—they can trust. To make it home, they will have to trust one another—even when fear and old secrets threaten to split them apart.',
    excerpt: `“Holy...” Bean whispers. Together we stare out into the night, miles away to where the meteor fell. A faint yellow glow gradually fades until the whole area is blanketed in darkness again.

“I bet it’s”—Fynn wheezes—“an extraterrestrial.”

“Did you watch Alien again?” Beans asks.

Fynn sits up on another wheeze. “Maybe.”

“A UFO,” Rocky agrees. “And they’re going to suck our brains right out of our heads.”`,
    effort: 55,
    length: 60,
    color: '#d87356',
    ink: '#fff1d2',
    icon: 'telescope',
    mood: ['adventure', 'weird', 'mystery'],
    storyLink: 'https://read.amazon.com/sample/0316704121?clientId=share&asin=0316704121&revisionId=&format=4&depth=1',
    callNumber: 'F Gre',
    sublocation: 'Realistic Fiction'
  },
  {
    id: 'unplugged',
    title: 'Unplugged',
    author: 'Gordon Korman',
    bookTitle: '(No Wifi, Big Secrets)',
    cover: '/covers/unplugged.jpg',
    character: 'Jett',
    theme: 'off-grid mystery',
    hook: 'A tech-free retreat hides a very strange secret.',
    synopsis: 'Jett Baranov, the spoiled son of a billionaire tech genius, is sent to the Oasis wellness retreat after one prank too many. There are no phones, no screens, terrible food, and a long list of rules—basically Jett’s worst nightmare. But when he and the other kids notice strange activity in the nearby woods, boredom gives way to a mystery no adult seems willing to explain. The more Jett investigates, the more he realizes that the retreat’s rules may be hiding something bigger than bad food and no Wi-Fi. Jett may be disconnected from the internet, but uncovering what Oasis is hiding could require every clever trick he knows.',
    excerpt: `“I don’t have a spiritual life,” I reply stubbornly. “Some crazy lady stole it along with my phone.”

If Ivory is offended by that, she doesn’t let on. “Hostility is the byproduct of a mind out of balance,” she says understandingly.

“At least I have a mind,” I mumble under my breath.

“Don’t be rude.” Matt puts an arm around my shoulders in an attempt to calm me down. “Take it easy, kid. You’re not in California anymore.”

I shrug him off violently. “Yeah, really? What tipped you off? The swamp gas? The possum B.O.? The fact that we haven’t seen an In-N-Out Burger for two hundred miles?”`,
    effort: 44,
    length: 50,
    color: '#16A34A',
    ink: '#f0fdf4',
    icon: 'compass',
    mood: ['mystery', 'adventure'],
    storyLink: ' https://read.amazon.com/sample/0062798901?clientId=share',
    callNumber: 'F Kot',
    sublocation: 'Literature Circle'
  },
  {
    id: 'intergalactic',
    title: 'Stowaway',
    bookTitle: '(The Intergalactic Reach)',
    author: 'John David Anderson',
    cover: '/covers/stowaway.jpg',
    character: 'Leo',
    theme: 'high-stakes space',
    hook: 'In space, hiding is only the beginning.',
    synopsis: 'When scientists discover that a rare mineral found on Earth is the most valuable resource in the universe, alien civilizations arrive with promises of technology and protection. Their deal soon pulls Earth into a brutal intergalactic war. After an attack kills his mother, Leo flees the planet with his scientist father and younger brother—only for their ship to be ambushed and his father kidnapped. Stranded far from home, Leo hides aboard a mercenary pirate ship and must learn how to survive among people he never expected to trust. If he cannot convince them to help, he may lose the last pieces of his family forever.',
    excerpt: 'Leo’s world had always been a little alien. He was only two when the Aykari made first contact. He had no recollection of a world without spaceships and jump drives and translator chips. He had no idea what coal was used for or how paper was made, but by age eight he knew how an artificial gravity generator worked (sort of) and could list the steps required to terraform a hostile planet.',
    //// exerpt could be slitghly more interesting or longer
    effort: 58,
    length: 61,
    color: '#526b98',
    ink: '#eef0ff',
    icon: 'telescope',
    mood: ['adventure', 'weird'],
    storyLink: 'https://read.amazon.com/sample/0062985957?clientId=share&asin=0062985957&revisionId=&format=4&depth=1',
    callNumber: 'F And',
    sublocation: 'Science Fiction'
  },
  {
    id: 'warcross',
    title: 'Warcross',
    bookTitle: '(Glitch into the Game)',
    author: 'Marie Lu',
    cover: '/covers/warcross.jpg',
    character: 'Emika',
    theme: 'virtual reality heist',
    hook: 'She hacked the world’s biggest game—and became the target.',
    synopsis: 'Ten years after the virtual-reality game Warcross takes over the world, teenage bounty hunter Emika Chen is broke, desperate, and running out of time. She hacks into the international championship hoping to make quick cash—but accidentally glitches herself into the live broadcast and becomes an overnight sensation. Instead of arresting her, the game’s mysterious creator, Hideo Tanaka, flies Emika to Tokyo and hires her to uncover a dangerous security threat hidden inside the tournament. Competing with the world’s best players while working as a secret spy, Emika discovers a plot that could control far more than a game. Every round brings Emika closer to the danger—and forces her to question whether the people around her are players, allies, or something else. The closer she gets to the truth, the harder it becomes to know whom she can trust.',
    excerpt: `For the millions who log in every day, Warcross isn't just a game—it's a way of life. The neon lights of Tokyo pulse around me as my NeuroLink boots up. One bad glitch, and I'm out of the system for good. It's too damn cold of a day to be out on a hunt. I shiver, tug my scarf up higher over my mouth, and slam my boot down on my electric skateboard. My bright, rainbow-dyed hair whips across my face. Seventy-two hours to pay or vacate—I'm almost three months behind on rent. Unless I can get my hands on $3,450, I'll be homeless by the end of the week. That's where I come in. I'm a bounty hunter, one of many in Manhattan, and I'm fighting to capture my target before another hunter can.`,
    // Interisting !
    effort: 48,
    length: 55,
    color: '#E11D48',
    ink: '#fff1f2',
    icon: 'flame',
    mood: ['adventure', 'weird'],
    storyLink: 'https://read.amazon.com/sample/B01MZ88EXL?clientId=share',
    callNumber: 'F Lu',
    sublocation: 'Science Fiction'
  },
  {
    id: 'kotlc',
    title: 'Keeper of the Lost Cities',
    bookTitle: '(A hidden Magical World)',
    author: 'Shannon Messenger',
    cover: '/covers/keeper-of-the-lost-cities.jpg',
    character: 'Sophie',
    theme: 'hidden identity',
    hook: 'She was never meant for the human world.',
    synopsis: 'Twelve-year-old Sophie Foster has spent her whole life hiding an impossible secret: she can hear everyone’s thoughts. Then she meets Fitz, a mysterious boy who can do the same thing, and learns that she does not belong to the human world at all. In a hidden civilization filled with strange abilities, powerful families, and dangerous rules, Sophie finally finds people like her—but not everyone is happy she has returned. As Sophie learns the rules of her new world, she discovers that fitting in may be just as dangerous as standing out. Someone buried secrets inside her memories, and there are enemies willing to do anything to uncover them. Sophie must discover who she really is before those secrets put everyone she cares about in danger.',
    excerpt: `Kids’ thoughts were stinging, high-pitched needles—so many at once, it was like an angry porcupine attacking her brain. Then she remembered she wasn’t alone. His hands were at his forehead, and his face wore the same pained expression.

“Did you just... hear that?” he asked.

“Who are you?” she whispered.

His eyes widened. “Are you a telepath? It’s okay. You don’t have to be afraid. I’m one too.”`,
    // Kinda doesn't make sense so not interesting
    effort: 50,
    length: 70,
    color: '#3B82F6',
    ink: '#eff6ff',
    icon: 'crown',
    mood: ['adventure', 'weird'],
    storyLink:'https://read.amazon.com/sample/1442445947?clientId=share',
    callNumber: 'F Mes',
    sublocation: 'Fantasy'
  },
  {
    id: 'global-race',
    title: 'City Spies',
    bookTitle: '(A Global Race)',
    author: 'James Ponti',
    cover: '/covers/city-spies.jpg',
    character: 'five undercover agents',
    theme: 'spy-kid vibes',
    hook: 'They’re kids with a world-sized secret.',
    synopsis: 'Sara Martinez is a brilliant young hacker facing serious consequences when a mysterious man offers her an unexpected escape: join a secret team of kid spies working for British intelligence. From their hidden base in Scotland, the City Spies train in surveillance, code breaking, sleight of hand, and getting into places adults cannot. Sara—now code-named Brooklyn—is thrown into a high-stakes mission at an international youth summit in Paris, where one mistake could endanger thousands of people. The team may have amazing skills, but they are still learning how to work together under pressure. To stop the attack, Sara must learn to trust a team of strangers before time runs out.',
    excerpt: `“But all I did was—”

“What?” he interrupted. “Hack into the computer network for the entire juvenile justice system of New York City? Is that what you were going to say? Because that’s not what I’d call an ‘all I did’ situation.”

“I know, but I was only trying to—”

“It doesn’t matter what you were trying to do,” he said. “All that matters is what you did. You’re lucky you’re twelve. If you were thirteen, they probably would have bumped you up to a high court to make an example out of you.”`,
    effort: 56,
    length: 52,
    color: '#5d6da8',
    ink: '#f1f2ff',
    icon: 'telescope',
    mood: ['adventure', 'mystery'],
    storyLink: 'https://read.amazon.com/sample/1534414924?clientId=share',
    callNumber: 'F Pon',
    sublocation: 'Mystery'
  },
  {
    id: 'mystery-road-trip',
    title: 'Paper Towns',
    bookTitle: '(The Mystery Road-Trip)',
    author: 'John Green',
    cover: '/covers/paper-towns.jpg',
    character: 'Margo and Quentin',
    theme: 'deep story',
    hook: 'To find Margo, Quentin must follow the clues.',
    synopsis: 'Quentin Jacobsen has spent years admiring his fearless, unpredictable neighbor Margo Roth Spiegelman from a distance. One night, she climbs through his window and recruits him for an elaborate midnight mission of revenge, giving Quentin the adventure he has always imagined. By morning, Margo has vanished—but she has left behind a trail of strange clues that seem meant for him. As Quentin follows them with his friends, the search becomes a road trip through the stories people tell about Margo. He begins to realize that the real Margo may be very different from the legend everyone created. Is he solving a mystery, or chasing someone who does not want to be found?',
    excerpt: `“I need your car,” she explained.

“I don’t have a car,” I said, which was something of a sore point for me.

“Well, I need your mom’s car.”

“You have your own car,” I pointed out.

Margo puffed out her cheeks and sighed. “Right, but the thing is that my parents have taken the keys to my car and locked them inside a safe, which they put under the bed, and Myrna Mountweazel is sleeping inside their room. And Myrna Mountweazel has a freaking aneurysm whenever she catches sight of me. I mean, I could totally sneak in there and steal the safe and crack it and get my keys out and drive away, but the thing is that it’s not even worth trying—.”`,
    effort: 63,
    length: 62,
    color: '#8b5d82',
    ink: '#fff1f5',
    icon: 'map',
    mood: ['mystery', 'comfort'],
    storyLink:'https://read.amazon.com/sample/014241493X?clientId=share',
    callNumber: 'F Gre',
    sublocation: 'Romance'
  },
  {
    id: 'looks-deceiving',
    title: 'One of Us Is Lying',
    bookTitle: '(Looks Can Be Deceiving)',
    author: 'Karen M. McManus',
    cover: '/covers/one-of-us-is-lying.jpg',
    character: 'Bronwyn and Simon',
    theme: 'full story',
    hook: 'Five walked in. Only one knows the truth.',
    synopsis: 'Five students walk into detention at Bayview High, but only four leave alive. The victim is Simon, creator of the school’s notorious gossip app, and he was about to reveal damaging secrets about every student in the room. Suddenly the athlete, the brain, the criminal, and the princess are all suspects—with motives they desperately want to hide. As the investigation closes in, their carefully built reputations begin to crack. Each student has a reason to lie, but the clues do not point neatly in one direction. One of them may be lying about what happened, but someone outside the room could be controlling the story.',
    excerpt: `“Old news, Bronwyn,” says a voice over my shoulder. “Wait till you see tomorrow’s post.”

Damn. I hate getting caught reading About That, especially by its creator. I lower my phone and slam my locker shut.

“Whose lives are you ruining next, Simon?”

Simon falls into step beside me as I move against the flow of students heading for the exit. “It’s a public service,” he says with a dismissive wave. “You tutor Reggie Crawley, don’t you? Wouldn’t you want to know he has a camera in his bedroom?”

I don’t bother answering. Me getting anywhere near the bedroom of perpetual stoner Reggie Crawley is about as likely as Simon growing a conscience.`,
    effort: 70,
    length: 66,
    color: '#3e8d87',
    ink: '#f0ffe8',
    icon: 'circle-help',
    mood: ['mystery', 'weird'],
    storyLink:'https://read.amazon.com/sample/1524714755?clientId=share',
    callNumber: 'F McM',
    sublocation: 'Mystery'
  },
  {
    id: 'cosmic',
    title: 'The Inexplicable Logic of My Life',
    bookTitle: '(A Cosmic Deep Dive)',
    author: 'Benjamin Alire Sáenz',
    cover: '/covers/inexplicable-logic.jpg',
    character: 'Sal and Samantha',
    theme: 'family and belonging',
    hook: 'Family, grief, and identity bring no easy answers.',
    synopsis: 'Sal has always felt secure with his adoptive father, Vicente, their loud and loving Mexican American family, and his sharp-witted best friend, Samantha. But during senior year, grief and uncertainty begin changing the way Sal sees himself—and he starts reacting with anger he does not recognize. While Samantha faces a crisis of her own and Vicente tries to protect them both, Sal searches for answers about his biological family and the person he wants to become. His friendships and family relationships are tested as he tries to understand what he is feeling. Growing up may mean accepting that love cannot prevent pain, but it can help people survive it. If Sal is no longer the “good kid” everyone expects, who will he choose to be?',
    excerpt: `“My dad is a man. He has a name. His name is Vicente. So if you want to call him something, call him by his name. And he’s not a faggot.”

Sam just looked at me. I looked back at her.

“Well, this is new,” she said. “What happened to the good boy? I never knew you had it in you to punch a guy.”

“I didn’t either,” I said.

Sam smiled at me. It was kind of a strange smile. I looked down at Enrique. I tried to help him up, but he wasn’t having any of it.

“Fuck you,” he said as he picked himself up off the ground.

Sam and I watched as he walked away. He turned around and flipped me the bird. I was a little stunned. I looked at Sam.

“Maybe we don’t always know what we have inside us.”`,
    // if it is a chill book, then interesting
    effort: 20,
    length: 65,
    color: '#9b6990',
    ink: '#fff0f4',
    icon: 'heart',
    mood: ['comfort', 'weird'],
    storyLink:'https://read.amazon.com/sample/0544586506?clientId=share',
    callNumber: 'F Sea',
    sublocation: 'Realistic Fiction'
  },
  {
    id: 'heartfelt',
    title: 'Fish in a Tree',
    bookTitle: '(A Heartfelt Sprint)',
    author: 'Lynda Mullaly Hunt',
    cover: '/covers/fish-in-a-tree.jpg',
    character: 'Ally',
    theme: 'standing out',
    hook: 'Ally is learning that different can be powerful.',
    synopsis: 'Sixth-grader Ally is smart, creative, and an expert at hiding the fact that reading feels nearly impossible to her. She would rather cause trouble and get sent to the office than let her classmates discover what she cannot do. When a patient new teacher, Mr. Daniels, recognizes her dyslexia, Ally begins to understand that struggling with words does not mean she is unintelligent. Small successes and unexpected friendships give Ally new reasons to stop hiding, even when school still feels overwhelming. With unexpected friends beside her, she must decide whether she is brave enough to stop hiding—and prove that the way her mind works may be one of her greatest strengths.',
    excerpt: `“Ally,” he says. “I think you gave her that card to tell her you’re sorry she has to leave us to go have some dumb baby. She’s probably really sad. I feel sorry for her, too.”

“Oliver?” Mrs. Silver asks. “Is there a reason you’re out here?”

“Yeah! I was going to... um... I was going to go to the boys’ room. Yeah. That’s it.” And off he runs.

“Can I just go now?” I blurt out, feeling like the job of just standing there is something I can’t do for another second.

She shakes her head a bit as she speaks. “I just don’t get it. Why in the world would you give a pregnant woman a sympathy card?”`,
    effort: 38,
    length: 52,
    color: '#e68a82',
    ink: '#fff2eb',
    icon: 'heart',
    mood: ['comfort', 'adventure'],
    storyLink:'https://read.amazon.com/sample/0142426423?clientId=share',
    callNumber: 'F Hun',
    sublocation: 'Realistic Fiction'
  },
  {
    id: 'royal',
    title: 'Tokyo Ever After',
    bookTitle: '(A Royal Journey)',
    author: 'Emiko Jean',
    cover: '/covers/tokyo-ever-after.jpg',
    character: 'Izumi',
    theme: 'culture shock',
    hook: 'An ordinary teen. A royal secret. No easy escape.',
    synopsis: 'Izumi Tanaka has always felt caught between her Japanese heritage and her everyday life as a teenager in Northern California. Then she discovers that the father she has never known is the Crown Prince of Japan—and that makes her a princess. Whisked to Tokyo, Izumi must learn royal traditions, survive relentless reporters, and face relatives who question whether she belongs in the imperial family. Between palace rules, public attention, and a forbidden romance with her bodyguard, Izumi has little time to figure out who she wants to be. Izumi finally has a chance to learn where she comes from, but can she become a princess without losing herself?',
    excerpt: `MOM: Don’t get out of the car.

Too late. Gravel crunches under my feet. Car doors slam. Lights flash. Then I hear it. My name.

“Princess Izumi, over here.”

Like an idiot, I turn. Another flash. I’m temporarily blinded. I blink. My vision clears. In front of me is a pack of reporters. Most are Asian. A few are white. I focus on one of their badges. Press, it says. Tokyo Tattler.

“Oh my God,” Noora exclaims.

She’s in a similar frozen state. Keys jangle from her hands, her mouth is open, and her jaw is totally unhinged. I’ve never seen her struck speechless. It’s glorious—but there’s no time to appreciate the novelty. I am under siege.

“Will you be traveling to Japan?”

“How was it growing up without your father?”

“Have you known who your father is your whole life?”`,
    effort: 30,
    length: 65,
    color: '#7158a5',
    ink: '#fff2df',
    icon: 'crown',
    mood: ['weird', 'comfort'],
    storyLink:'https://read.amazon.com/sample/1250766621?clientId=share',
    callNumber: 'F Jean',
    sublocation: 'Romance'
  },
];

const moodData: Array<{ id: MoodId; label: string; note: string; glyph: string; accent: string; icon: typeof Map }> = [
  { id: 'adventure', label: 'Adventure / escape', note: 'Take the long way home.', glyph: '👽', accent: '#5f76c9', icon: Telescope },
  { id: 'comfort', label: 'Comfort / relax', note: 'A warm place to land.', glyph: '💗', accent: '#df8175', icon: Heart },
  { id: 'weird', label: 'Surprising / weird', note: 'Turn the page sideways.', glyph: '🗺️', accent: '#e9a04a', icon: Map },
  { id: 'mystery', label: 'Thrilling / mystery', note: 'Follow the clue.', glyph: '🔍', accent: '#3f8e82', icon: CircleHelp },
];

const reflectionEmojiGroups = [
  { label: 'Feelings', emojis: ['❤️', '😂', '😱', '😭', '😌', '🥹'] },
  { label: 'Thinking', emojis: ['🤔', '🧠', '💡', '😮', '✨', '📚'] },
  { label: 'Story energy', emojis: ['🔥', '🚀', '👏', '🫶', '🎭', '😴'] },
  { label: 'Story worlds', emojis: ['🌈', '👽', '🗺️', '🌙', '🦋', '👑'] },
];

const sampleSets: Record<MoodId, string[]> = {
  adventure: ['intergalactic', 'warcross', 'kotlc'],
  weird: ['do-over', 'aliens', 'unplugged'],
  mystery: ['global-race', 'mystery-road-trip', 'looks-deceiving'],
  comfort: ['cosmic', 'heartfelt', 'royal'],
};

const queryClient = new QueryClient();
const defaultSession: Session = { mood: null, savedStory: null, savedStories: [], reflection: '', reflectionEmojis: [] };
const SessionContext = createContext<{
  session: Session;
  setMood: (mood: MoodId) => void;
  saveStory: (id: string) => void;
  removeStory: (id: string) => void;
  saveReflection: (text: string, emojis: string[]) => void;
  resetSession: () => void;
}>({

  session: defaultSession,
  setMood: () => undefined,
  saveStory: () => undefined,
  removeStory: () => undefined,
  saveReflection: () => undefined,
  resetSession: () => undefined,
});

function useSession() {
  return useContext(SessionContext);
}

function readSession(): Session {
  try {
    const stored = window.localStorage.getItem('bookhook-session');
    if (!stored) return defaultSession;
    const parsed = JSON.parse(stored);
    const savedStories = (Array.isArray(parsed.savedStories)
      ? parsed.savedStories
      : typeof parsed.savedStory === 'string'
        ? [parsed.savedStory]
        : [])
      .filter((item: unknown): item is string => typeof item === 'string')
      .slice(0, 3);
    return {
      ...defaultSession,
      ...parsed,
      savedStory: savedStories[0] ?? null,
      savedStories,
      reflectionEmojis: Array.isArray(parsed.reflectionEmojis) ? parsed.reflectionEmojis : [],
    };
  } catch {
    return defaultSession;
  }
}

function AppProvider({ children }: { children: ReactNode }) {
  const [session, setSession] = useState<Session>(readSession);
  const update = (next: Session) => {
    setSession(next);
    window.localStorage.setItem('bookhook-session', JSON.stringify(next));
  };
  const value = useMemo(() => ({
    session,
    setMood: (mood: MoodId) => update({ ...session, mood }),
    saveStory: (id: string) => {
      if (session.savedStories.includes(id) || session.savedStories.length >= 3) return;
      const savedStories = [...session.savedStories, id];
      update({ ...session, savedStory: savedStories[0] ?? null, savedStories });
    },
    removeStory: (id: string) => {
      const savedStories = session.savedStories.filter((storyId) => storyId !== id);
      update({ ...session, savedStory: savedStories[0] ?? null, savedStories });
    },
    saveReflection: (reflection: string, reflectionEmojis: string[]) => update({ ...session, reflection, reflectionEmojis }),
    resetSession: () => update(defaultSession),
  }), [session]);
  return <SessionContext.Provider value={value}>{children}</SessionContext.Provider>;
}

function Logo() {
  return (
    <Link href="/" className="flex items-center gap-2.5 w-fit" data-testid="link-logo">
      <span className="grid h-9 w-9 place-items-center rounded-xl bg-secondary text-foreground shadow-[0_3px_0_hsl(16_72%_40%)]">
        <BookOpen size={18} strokeWidth={2.5} />
      </span>
      <span className="brand-mark text-[1.65rem] font-extrabold tracking-tight">bookhook</span>
    </Link>
  );
}

function Topbar() {
  const [location] = useLocation();
  const { session } = useSession();
  const isSession = location !== '/';
  const step = location.startsWith('/mood') ? 1 : location.startsWith('/moments') ? 2 : location.startsWith('/story') ? 3 : location.startsWith('/reflection') ? 4 : location.startsWith('/complete') ? 5 : 0;
  return (
    <header className="relative z-10 border-b border-border/70">
      <div className="content-width flex min-h-[76px] items-center justify-between gap-4">
        <Logo />
        <div className="flex items-center gap-3">
          <Link href="/disclaimer" className="inline-flex rounded-full border border-border/80 bg-card/60 px-3 py-2 text-[10px] font-bold uppercase tracking-[.12em] text-muted-foreground transition-colors hover:border-primary hover:text-primary" data-testid="link-disclaimer">
            <span className="sm:hidden">Legal notes</span><span className="hidden sm:inline">Disclaimer &amp; copyright</span>
          </Link>
          {isSession && (
            <>
            <div className="hidden text-right sm:block">
              <p className="text-[10px] font-bold uppercase tracking-[.18em] text-muted-foreground">Your reading drawer</p>
              <p className="text-xs font-semibold text-foreground">{session.mood ? moodData.find((m) => m.id === session.mood)?.label : 'One good story'}</p>
            </div>
            <div className="page-dots flex items-center gap-1.5" aria-label={`Step ${step} of 5`}>
              {[1, 2, 3, 4, 5].map((dot) => <span key={dot} className={dot <= step ? 'active' : ''} />)}
            </div>
            </>
          )}
        </div>
      </div>
    </header>
  );
}

function DecorativeBooks() {
  return (
    <div className="pointer-events-none absolute bottom-[-2rem] right-[-1rem] hidden h-56 w-52 md:block" aria-hidden="true">
      <div className="absolute bottom-0 right-2 h-28 w-40 rotate-[-8deg] rounded-r-2xl bg-[#526b98] shadow-[8px_10px_0_hsl(244_30%_25%_/_0.12)]" />
      <div className="absolute bottom-10 right-8 h-36 w-32 rotate-[8deg] rounded-r-2xl bg-[#e9a04a] shadow-[7px_8px_0_hsl(244_30%_25%_/_0.11)]" />
      <div className="absolute bottom-4 right-20 h-32 w-24 rotate-[-18deg] rounded-r-2xl bg-[#3e8d87] shadow-[6px_9px_0_hsl(244_30%_25%_/_0.11)]" />
      <div className="absolute bottom-28 right-4 h-14 w-14 rounded-full border-4 border-secondary/70 bg-[#fff1d2] floaty" />
    </div>
  );
}

function Home() {
  const [, setLocation] = useLocation();
  const { session } = useSession();
  return (
    <main className="page-shell">
      <Topbar />
      <div className="content-width relative flex min-h-[calc(100dvh-77px)] flex-col justify-center py-16">
        <div className="max-w-4xl animate-rise">
          <div className="mb-7 flex items-center gap-3 font-bold uppercase tracking-[.2em] text-destructive text-[15px]">
            <span className="h-px w-9 bg-secondary" /> A little drawer of stories
          </div>
          <h1 className="display max-w-3xl text-[clamp(3.75rem,9vw,8.2rem)] font-extrabold leading-[.87] text-[#23243e]">
            Find a story<br /><span className="text-primary">that gets you.</span>
          </h1>
          <p className="mt-8 max-w-md text-[22px] text-secondary-foreground">
            Bookhook listens for your reading mood, then slides three curious possibilities across the counter. No homework energy. Just a good first page.
          </p>
          <button onClick={() => setLocation('/mood')} className="button-primary mt-9 inline-flex items-center gap-3 rounded-full bg-primary px-7 py-4 text-sm font-bold text-primary-foreground" data-testid="button-start-reading">
            {session.mood ? 'Start a fresh session' : 'Open the drawer'} <ArrowRight size={18} />
          </button>
        </div>
        <div className="mt-20 grid max-w-2xl grid-cols-3 gap-8 border-t border-border pt-5 text-sm text-muted-foreground animate-rise stagger-2">
          <div className="text-[20px] text-destructive"><span className="mb-2 block text-2xl font-bold text-foreground">01</span><span>Choose a feeling</span></div>
          <div className="text-[20px] text-destructive"><span className="mb-2 block text-2xl font-bold text-foreground">02</span><span>Peek at a moment</span></div>
          <div className="text-[20px] text-destructive"><span className="mb-2 block text-2xl font-bold text-foreground">03</span><span>Keep what clicks</span></div>
        </div>
        <DecorativeBooks />
        <div className="absolute right-[33%] top-[19%] hidden max-w-[180px] rotate-[5deg] rounded-lg p-4 hand-note text-xs leading-relaxed text-foreground lg:block">
          A story is waiting for the exact version of you that showed up today.
          <span className="mt-2 block text-[10px] font-bold uppercase tracking-wider text-secondary">— the bookhook note</span>
        </div>
      </div>
    </main>
  );
}

function MoodPage() {
  const [, setLocation] = useLocation();
  const { session, setMood } = useSession();
  const [selected, setSelected] = useState<MoodId | null>(session.mood);
  const chooseMood = (id: MoodId) => { setSelected(id); setMood(id); };
  return (
    <main className={`page-shell mood-${selected ?? 'default'}`}>
      <Topbar />
      <div className="content-width py-12 sm:py-20">
        <button onClick={() => setLocation('/')} className="button-quiet mb-14 inline-flex items-center gap-2 rounded-full px-3 py-2 text-sm font-bold text-muted-foreground" data-testid="button-back-home"><ArrowLeft size={16} /> Back to the shelf</button>
        <div className="max-w-3xl animate-rise">
          <p className="mb-4 font-bold uppercase tracking-[.2em] text-secondary text-[16px]">First, check the weather inside</p>
          <h1 className="display text-[clamp(2.8rem,7vw,6rem)] font-extrabold leading-[.9]">What should reading<br /><span className="text-primary">feel like today?</span></h1>
          <p className="mt-7 max-w-lg text-[25px] text-sidebar">There is no wrong shelf. Pick the door that sounds most like you right now.</p>
        </div>
        <div className="mt-12 grid max-w-4xl gap-4 sm:grid-cols-2">
          {moodData.map(({ id, label, note, glyph, accent, icon: Icon }, index) => (
            <button key={id} onClick={() => chooseMood(id)} className={`mood-card group relative flex min-h-[150px] items-end justify-between overflow-hidden rounded-2xl border-2 p-6 text-left ${selected === id ? 'selected' : 'border-border bg-card'}`} style={{ animationDelay: `${index * 80}ms` }} data-testid={`button-mood-${id}`}>
              <span className="absolute right-[-14px] top-[-26px] h-32 w-32 rounded-full opacity-20 transition-transform group-hover:scale-125" style={{ background: accent }} />
              <span className="relative">
                <span className="mb-3 flex items-center gap-2"><span className="grid h-10 w-10 place-items-center rounded-xl text-foreground" style={{ background: `${accent}55` }}><Icon size={20} /></span><span className="text-xl" aria-hidden="true">{glyph}</span></span>
                <span className="display block text-xl font-bold capitalize">{label}</span>
                <span className="mt-1 block text-sm text-muted-foreground">{note}</span>
              </span>
              <span className={`relative grid h-9 w-9 place-items-center rounded-full transition-colors ${selected === id ? 'bg-primary text-primary-foreground' : 'bg-muted text-muted-foreground'}`}><ChevronRight size={18} /></span>
            </button>
          ))}
        </div>
        <div className="mt-10 flex items-center justify-between border-t border-border pt-6">
          <span className="text-ring font-semibold text-[20px]">{selected ? 'Good. We know where to look.' : 'Pick one to open the next drawer.'}</span>
          <button disabled={!selected} onClick={() => setLocation('/moments')} className="button-primary inline-flex items-center gap-2 rounded-full bg-primary px-6 py-3 text-sm font-bold text-primary-foreground disabled:cursor-not-allowed disabled:opacity-40 disabled:shadow-none" data-testid="button-continue-mood">Show me a moment <ArrowRight size={17} /></button>
        </div>
      </div>
    </main>
  );
}

function IconForStory({ story, size = 22 }: { story: Story; size?: number }) {
  const Icon = story.icon === 'map' ? Map : story.icon === 'telescope' ? Telescope : story.icon === 'heart' ? Heart : story.icon === 'crown' ? Crown : story.icon === 'circle-help' ? CircleHelp : Flame;
  return <Icon size={size} />;
}

function MomentsPage() {
  const [, setLocation] = useLocation();
  const { session } = useSession();
  const [revealed, setRevealed] = useState<string[]>([]);
  const mood = session.mood ?? 'adventure';
  const choices = sampleSets[mood].map((id) => stories.find((story) => story.id === id)).filter((story): story is Story => Boolean(story));
  return (
    <main className={`page-shell mood-${mood}`}>
      <Topbar />
      <div className="content-width py-12 sm:py-16">
        <div className="mb-12 flex items-end justify-between gap-6 animate-rise">
          <div>
            <p className="mb-4 text-xs font-bold uppercase tracking-[.2em] text-secondary">A small taste before a title</p>
            <h1 className="display max-w-2xl text-[clamp(2.8rem,6vw,5.7rem)] font-extrabold leading-[.9]">Three moments.<br /><span className="text-primary">One might hook you.</span></h1>
          </div>
          <div className="hidden rounded-2xl bg-accent/10 px-4 py-3 text-right text-sm text-accent sm:block"><span className="mb-1 block text-[10px] font-bold uppercase tracking-wider">Your weather</span><span className="font-bold capitalize">{moodData.find((item) => item.id === mood)?.label}</span></div>
        </div>
        <div className="grid gap-5 lg:grid-cols-3">
          {choices.map((story, index) => {
            const isRevealed = revealed.includes(story.id);
            return (
              <article key={story.id} className={`story-card ink-card relative flex min-h-[420px] flex-col overflow-hidden rounded-[1.5rem] ${isRevealed ? 'reveal-card' : ''}`} style={{ animationDelay: `${index * 100}ms` }} data-testid={`card-moment-${story.id}`}>
                <div className="relative flex h-24 items-start justify-between overflow-hidden p-5" style={{ background: story.color, color: story.ink }}>
                  <span className="book-spine h-16 bg-black/10" />
                  <span className="rounded-full border border-current/30 px-2.5 py-1 text-[10px] font-bold uppercase tracking-wider">{`0${index + 1}`} / moment</span>
                  <div className="absolute bottom-[-24px] right-5 h-20 w-20 rounded-full border-[12px] border-current/20" />
                </div>
                <div className="flex flex-1 flex-col p-6">
                  <div className="mb-5 flex items-center gap-2 text-xs font-bold uppercase tracking-wider text-muted-foreground"><span className="grid h-8 w-8 place-items-center rounded-lg bg-muted text-foreground"><IconForStory story={story} size={16} /></span> A {story.theme} story</div>
                  <p className="quote-mark mb-1">“</p>
                  <p className="whitespace-pre-line text-[1.08rem] leading-[1.6] text-foreground">{story.excerpt}</p>
                  <div className="mt-auto pt-7">
                    {isRevealed ? (
                      <div className="animate-rise border-t border-border pt-4">
                        <img src={story.cover} alt={`${story.title} cover`} className="cover-reveal mb-4 h-32 w-24 rounded-lg bg-white/60 object-contain shadow-md" />
                        <p className="text-[10px] font-bold uppercase tracking-[.16em] text-secondary">The book is</p>
                        <h2 className="display mt-1 text-2xl font-bold leading-tight">{story.title}</h2>
                        <p className="mt-1 text-xs text-muted-foreground">{story.bookTitle} · {story.author}</p>
                        <button onClick={() => setLocation(`/story/${story.id}`)} className="mt-4 inline-flex items-center gap-2 rounded-full bg-primary px-4 py-2.5 text-xs font-bold text-primary-foreground" data-testid={`button-open-story-${story.id}`}>Open the story <ArrowRight size={15} /></button>
                      </div>
                    ) : (
                      <button onClick={() => setRevealed((current) => [...current, story.id])} className="group inline-flex items-center gap-2 rounded-full border-2 border-primary px-4 py-2.5 text-xs font-bold text-primary transition-colors hover:bg-primary hover:text-primary-foreground" data-testid={`button-reveal-${story.id}`}>Reveal book <span className="transition-transform group-hover:translate-x-1"><ArrowRight size={15} /></span></button>
                    )}
                  </div>
                </div>
              </article>
            );
          })}
        </div>
        <p className="mt-8 text-center text-sm text-muted-foreground">{revealed.length < 3 ? `${3 - revealed.length} more ${revealed.length === 2 ? 'book' : 'books'} tucked behind the moments.` : 'All three are out. Which one is tugging at your sleeve?'}</p>
        <button onClick={() => setLocation('/mood')} className="mx-auto mt-7 flex items-center gap-2 rounded-full border-2 border-secondary/45 bg-card/65 px-5 py-3 text-sm font-bold text-foreground shadow-sm transition-all hover:-translate-y-0.5 hover:border-secondary hover:bg-card focus-visible:outline-none focus-visible:ring-4 focus-visible:ring-secondary/25" data-testid="button-change-mood"><RotateCcw size={17} /> Choose a different feeling</button>
      </div>
    </main>
  );
}

function Indicator({ label, value, caption }: { label: string; value: number; caption: string }) {
  return (
    <div>
      <div className="mb-2 flex items-end justify-between"><span className="text-xs font-bold uppercase tracking-wider text-muted-foreground">{label}</span><span className="text-xs font-bold text-foreground">{caption}</span></div>
      <div className="range-track"><div className="range-fill" style={{ width: `${value}%` }} /></div>
    </div>
  );
}

function ShelfGuide({ story, compact = false }: { story: Story; compact?: boolean }) {
  const [open, setOpen] = useState(false);
  const callParts = story.callNumber.trim().split(/\s+/);
  const section = callParts[0] ?? 'F';
  const authorLetters = callParts.slice(1).join(' ');
  return (
    <section className={`shelf-guide rounded-2xl border border-secondary/25 bg-secondary/10 ${compact ? 'p-4' : 'p-5'}`} data-testid="shelf-guide">
      <div className="flex items-start gap-3">
        <span className="grid h-10 w-10 shrink-0 place-items-center rounded-xl bg-secondary text-foreground"><Map size={19} /></span>
        <div className="min-w-0 flex-1">
          <p className="text-[10px] font-bold uppercase tracking-[.16em] text-secondary">{compact ? 'Find it on the shelf' : 'Shelf reminder saved'}</p>
          <p className="mt-1 text-sm font-bold text-foreground">{story.sublocation}</p>
          <p className="mt-1 text-xs text-muted-foreground">Skyview Library · fiction section</p>
        </div>
        <div className="shrink-0 rounded-xl bg-card px-3 py-2 text-center shadow-sm" aria-label={`Call number ${story.callNumber}`}>
          <span className="block text-[9px] font-bold uppercase tracking-wider text-muted-foreground">Call no.</span>
          <span className="display text-lg font-extrabold tracking-wide text-primary">{story.callNumber}</span>
        </div>
      </div>
      <button type="button" onClick={() => setOpen((current) => !current)} aria-expanded={open} className="mt-4 inline-flex items-center gap-2 text-xs font-bold text-foreground underline decoration-secondary decoration-2 underline-offset-4 focus-visible:outline-none focus-visible:ring-4 focus-visible:ring-primary/20" data-testid="button-shelf-how-to">
        <ChevronRight size={15} className={`transition-transform ${open ? 'rotate-90' : ''}`} />
        How do I find this book? {open ? 'Hide guide' : 'Show me'}
      </button>
      {open && <div className="mt-4 grid gap-3 border-t border-secondary/20 pt-4 sm:grid-cols-3" data-testid="shelf-instructions">
        <p className="text-sm leading-relaxed text-foreground sm:col-span-3"><strong>Think of the call number as the book’s shelf address.</strong> The label on the book’s spine should match <strong>{story.callNumber}</strong>. Follow the steps below, in order.</p>
        <div className="flex gap-3"><span className="grid h-7 w-7 shrink-0 place-items-center rounded-full bg-primary text-xs font-extrabold text-primary-foreground">1</span><p className="text-xs leading-relaxed text-foreground">Walk to the <strong>{story.sublocation}</strong> sign. This is the smaller fiction area where this book lives—not the whole library.</p></div>
        <div className="flex gap-3"><span className="grid h-7 w-7 shrink-0 place-items-center rounded-full bg-primary text-xs font-extrabold text-primary-foreground">2</span><p className="text-xs leading-relaxed text-foreground">Look along the shelf for the <strong>F</strong> labels. <strong>F</strong> means <strong>Fiction</strong>. Start with F before looking at any letters after it.</p></div>
        <div className="flex gap-3"><span className="grid h-7 w-7 shrink-0 place-items-center rounded-full bg-primary text-xs font-extrabold text-primary-foreground">3</span><p className="text-xs leading-relaxed text-foreground">Now scan A–Z by the author’s last name—not the book title. Look for the nearby letters <strong>{authorLetters}</strong> on the spine.</p></div>
        <div className="rounded-xl bg-card/65 p-4 text-xs leading-relaxed text-foreground sm:col-span-3"><strong>For {story.title}:</strong> find the <strong>{story.sublocation}</strong> area, then the <strong>F</strong> fiction shelves, then the <strong>{authorLetters}</strong> author section. Books are shelved alphabetically, so keep moving from A toward Z until you reach those letters. Check the spine for the full call number.</div>
        <p className="text-xs leading-relaxed text-muted-foreground sm:col-span-3">If the shelf jumps from one letter to another, that is okay—check the neighboring spines. Still stuck? Show a librarian or teacher this card and say, “I’m looking for <strong>{story.callNumber}</strong> in <strong>{story.sublocation}</strong>.”</p>
      </div>}
    </section>
  );
}

function StoryPage() {
  const [, setLocation] = useLocation();
  const { id } = useParams<{ id: string }>();
  const { session, saveStory, removeStory } = useSession();
  const story = stories.find((item) => item.id === id) ?? stories[0];
  const shortlistCount = session.savedStories.length;
  const isSaved = session.savedStories.includes(story.id);
  const toggleShortlist = () => {
    if (isSaved) removeStory(story.id);
    else saveStory(story.id);
  };
  return (
    <main className={`page-shell mood-${session.mood ?? 'default'} book-immersive`} style={{ '--story-accent': story.color } as CSSProperties}>
      <Topbar />
      <div className="content-width py-10 sm:py-14">
        <button onClick={() => setLocation('/moments')} className="button-quiet mb-10 inline-flex items-center gap-2 rounded-full px-3 py-2 text-sm font-bold text-muted-foreground" data-testid="button-back-moments"><ArrowLeft size={16} /> Back to moments</button>
        <div className="grid gap-10 lg:grid-cols-[.8fr_1.2fr] lg:items-start">
          <div className="animate-rise">
            <div className="book-cover-stage relative mx-auto max-w-[360px] overflow-hidden rounded-[1.7rem] bg-card/55 p-3 shadow-[12px_14px_0_hsl(244_30%_25%_/_0.12)] lg:mx-0">
              <img src={story.cover} alt={`${story.title} by ${story.author}`} className="mx-auto block max-h-[540px] w-full rounded-[1.15rem] object-contain" />
            </div>
          </div>
          <div className="max-w-2xl animate-rise stagger-1">
            <p className="mb-4 text-xs font-bold uppercase tracking-[.2em] text-secondary">The one that caught your eye</p>
            <h2 className="display text-[clamp(2.8rem,6vw,5.7rem)] font-extrabold leading-[.88]">{story.title}</h2>
            <p className="font-bold text-foreground ml-[0px] text-[25px] mt-[7px]">{story.bookTitle}</p>
            <p className="text -sm text-sidebar mt-[25px] text-[27px]">by {story.author}</p>
            <div className="mt-8 border-l-4 border-secondary pl-5"><p className="text-xl font-semibold leading-relaxed text-foreground">“{story.hook}”</p></div>
            <p className="mt-8 text-base leading-[1.8] text-muted-foreground">{story.synopsis}</p>
            <div className="mt-9 grid gap-5 rounded-2xl bg-card/60 p-5 sm:grid-cols-2">
              <Indicator label="Story effort" value={story.effort} caption={story.effort < 45 ? 'easy landing' : story.effort < 60 ? 'middle gear' : 'full immersion'} />
              <Indicator label="Pacing" value={story.length} caption={story.length < 50 ? 'quick spark' : story.length < 60 ? 'steady pull' : 'deep dive'} />
            </div>
            <div className="mt-6"><ShelfGuide story={story} compact /></div>
            <div className="mt-8 flex flex-wrap items-center gap-3">
              <a href={story.storyLink.trim()} target="_blank" rel="noopener noreferrer" className="inline-flex items-center gap-2 rounded-full border-2 border-primary px-5 py-3 text-sm font-bold text-primary transition-colors hover:bg-primary hover:text-primary-foreground focus-visible:outline-none focus-visible:ring-4 focus-visible:ring-primary/25" data-testid="button-preview-chapter"><BookOpen size={17} /> Read first chapter <ExternalLink size={15} /></a>
              <button onClick={toggleShortlist} disabled={!isSaved && shortlistCount >= 3} className={`inline-flex items-center gap-2 rounded-full px-5 py-3 text-sm font-bold transition-colors focus-visible:outline-none focus-visible:ring-4 focus-visible:ring-primary/25 ${isSaved ? 'border-2 border-secondary bg-secondary/15 text-foreground hover:bg-secondary/25' : 'button-primary bg-primary text-primary-foreground disabled:cursor-not-allowed disabled:opacity-60'}`} data-testid="button-toggle-shortlist">
                {isSaved ? <Check size={17} /> : <Plus size={17} />}
                {isSaved ? 'Saved · Remove from shortlist' : shortlistCount >= 3 ? 'Shortlist full' : 'Add to shortlist'}
              </button>
            </div>
            <div className="mt-5 flex flex-wrap items-center gap-2.5 text-sm text-muted-foreground">
              <span className="rounded-full border border-border bg-muted/45 px-3.5 py-2 font-bold text-foreground" data-testid="text-shortlist-count">{shortlistCount} of 3 books saved</span>
              {shortlistCount > 0 && <button onClick={() => setLocation('/reflection')} className="inline-flex items-center gap-1.5 rounded-full bg-primary/10 px-3.5 py-2 font-bold text-primary underline decoration-secondary decoration-2 underline-offset-4 transition-colors hover:bg-primary/15" data-testid="button-final-choice">Continue with {shortlistCount} {shortlistCount === 1 ? 'pick' : 'picks'} <ArrowRight size={15} /></button>}
              <button onClick={() => setLocation('/moments')} className="rounded-full border border-border bg-card px-3.5 py-2 font-bold text-foreground underline decoration-secondary decoration-2 underline-offset-4 transition-colors hover:border-primary hover:text-primary" data-testid="button-browse-more">Browse more books</button>
            </div>
            <p className="mt-3 flex items-center gap-2 text-xs text-muted-foreground"><ExternalLink size={13} /> Opens an official preview in a new tab.</p>
            <p className="mt-5 text-xs text-muted-foreground">No commitment beyond the next page. You can always put it back.</p>
          </div>
        </div>
      </div>
    </main>
  );
}

function ReflectionPage() {
  const [, setLocation] = useLocation();
  const { session, saveReflection, removeStory } = useSession();
  const [text, setText] = useState(session.reflection);
  const [selectedEmojis, setSelectedEmojis] = useState<string[]>(session.reflectionEmojis);
  const selectedStories = session.savedStories.map((id) => stories.find((story) => story.id === id)).filter((story): story is Story => Boolean(story));
  const selectedStory = selectedStories[0];
  const toggleEmoji = (emoji: string) => {
    const next = selectedEmojis.includes(emoji) ? selectedEmojis.filter((item) => item !== emoji) : [...selectedEmojis, emoji];
    setSelectedEmojis(next);
    saveReflection(text.trim(), next);
  };
  const submit = () => { saveReflection(text.trim(), selectedEmojis); setLocation('/complete'); };
  const skip = () => { saveReflection(text.trim(), selectedEmojis); setLocation('/complete'); };
  return (
    <main className={`page-shell mood-${session.mood ?? 'default'}`}>
      <Topbar />
      <div className="content-width flex min-h-[calc(100dvh-77px)] items-center py-14">
        <div className="mx-auto w-full max-w-2xl animate-rise">
          <div className="mb-12 text-center"><span className="mx-auto mb-6 grid h-14 w-14 place-items-center rounded-2xl bg-secondary/20 text-secondary"><PenLine size={25} /></span><p className="mb-4 text-xs font-bold uppercase tracking-[.2em] text-secondary">A tiny bookmark for later</p><h1 className="display text-[clamp(2.8rem,7vw,5.5rem)] font-extrabold leading-[.9]">What do you think<br /><span className="text-primary">of your book?</span></h1><p className="mx-auto mt-6 max-w-md text-[18px] font-bold text-muted-foreground">Write a thought about the book you chose. A reaction, question, favorite moment, or emoji is perfect.</p></div>
          {selectedStories.length > 0 && <div className="mx-auto mb-7 max-w-lg rounded-2xl bg-card px-4 py-4 shadow-sm" data-testid="text-selected-story">
            <div className="flex items-center justify-between gap-3">
              <div><p className="text-[10px] font-bold uppercase tracking-wider text-muted-foreground">Your shortlist</p><p className="mt-1 text-sm font-bold text-foreground">{selectedStories.length} of 3 {selectedStories.length === 1 ? 'book' : 'books'} saved</p></div>
              <button onClick={() => setLocation('/moments')} className="text-xs font-bold text-primary underline decoration-secondary decoration-2 underline-offset-4" data-testid="button-add-another-book">Add another</button>
            </div>
            <div className="mt-3 grid gap-2 sm:grid-cols-2">
              {selectedStories.map((story, index) => <div key={story.id} className="flex items-center gap-3 rounded-xl border border-border/70 bg-background/45 p-2.5">
                <span className="grid h-9 w-9 shrink-0 place-items-center rounded-lg text-xs" style={{ background: story.color, color: story.ink }}><IconForStory story={story} size={16} /></span>
                <span className="min-w-0 flex-1"><span className="block truncate text-sm font-bold text-foreground">{story.title}</span><span className="block text-[10px] text-muted-foreground">{index === 0 ? 'Start here' : 'Also saved'}</span></span>
                <button type="button" onClick={() => removeStory(story.id)} className="shrink-0 px-1 text-[10px] font-bold text-muted-foreground underline underline-offset-2 hover:text-primary focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-primary/30" aria-label={`Remove ${story.title} from shortlist`} data-testid={`button-remove-shortlist-${story.id}`}>Remove</button>
              </div>)}
            </div>
          </div>}
          <label htmlFor="reflection-text" className="sr-only">Your reflection</label>
          <textarea id="reflection-text" value={text} onChange={(event) => setText(event.target.value)} placeholder="I keep thinking about..." rows={4} className="w-full resize-none rounded-2xl border-2 border-border bg-card/70 p-5 text-lg leading-relaxed outline-none transition-colors placeholder:text-muted-foreground/60 focus:border-primary focus-visible:ring-4 focus-visible:ring-primary/15" data-testid="input-reflection" />
          <div className="mt-5 rounded-2xl border border-border bg-card/55 p-5" data-testid="emoji-reflection-picker">
            <div className="flex items-center justify-between gap-3">
              <div><p className="text-sm font-bold text-foreground">Or show us how it felt</p><p className="mt-1 text-xs text-muted-foreground">Pick any emojis that match your reaction.</p></div>
              {selectedEmojis.length > 0 && <span className="text-xl" aria-label={`${selectedEmojis.length} emojis selected`}>{selectedEmojis.join(' ')}</span>}
            </div>
            <div className="mt-4 space-y-4">
              {reflectionEmojiGroups.map((group) => <div key={group.label}>
                <p className="mb-2 text-[10px] font-bold uppercase tracking-[.14em] text-muted-foreground">{group.label}</p>
                <div className="flex flex-wrap gap-2">{group.emojis.map((emoji) => {
                  const isSelected = selectedEmojis.includes(emoji);
                  return <button key={emoji} type="button" onClick={() => toggleEmoji(emoji)} aria-label={`${isSelected ? 'Remove' : 'Add'} ${emoji}`} aria-pressed={isSelected} className={`grid h-11 w-11 place-items-center rounded-xl border-2 text-2xl transition-all hover:-translate-y-0.5 focus-visible:outline-none focus-visible:ring-4 focus-visible:ring-primary/25 ${isSelected ? 'border-primary bg-primary/15 shadow-[0_3px_0_hsl(var(--primary)/.2)]' : 'border-transparent bg-muted/70 hover:border-border'}`} data-testid={`button-emoji-${emoji.codePointAt(0)}`}>{emoji}</button>;
                })}</div>
              </div>)}
            </div>
          </div>
          <div className="mt-7 flex flex-col items-stretch justify-between gap-4 sm:flex-row sm:items-center">
            <div className="flex flex-wrap items-center gap-2">
              <button onClick={() => selectedStory ? setLocation(`/story/${selectedStory.id}`) : setLocation('/moments')} className="button-quiet inline-flex items-center gap-2 rounded-full px-3 py-2 text-sm font-bold text-muted-foreground" data-testid="button-back-story"><ArrowLeft size={16} /> Back to story</button>
              <button onClick={skip} className="button-quiet inline-flex items-center gap-2 rounded-full px-3 py-2 text-sm font-bold text-muted-foreground" data-testid="button-skip-reflection">Skip for now <ChevronRight size={16} /></button>
            </div>
            <button onClick={submit} className="button-primary inline-flex items-center justify-center gap-2 rounded-full bg-primary px-6 py-3 text-sm font-bold text-primary-foreground" data-testid="button-submit-reflection">Finish my trail <ArrowRight size={17} /></button>
          </div>
        </div>
      </div>
    </main>
  );
}

function CompletePage() {
  const [, setLocation] = useLocation();
  const { session, resetSession } = useSession();
  const [paused, setPaused] = useState(false);
  const selectedStories = session.savedStories.map((id) => stories.find((item) => item.id === id)).filter((story): story is Story => Boolean(story));
  const story = selectedStories[0];
  const mood = moodData.find((item) => item.id === session.mood);
  const startNewMood = () => { resetSession(); setLocation('/mood'); };
  return (
    <main className={`page-shell mood-${session.mood ?? 'default'}`}>
      <Topbar />
      <div className="content-width flex min-h-[calc(100dvh-77px)] items-center justify-center py-14">
        <div className="w-full max-w-2xl text-center animate-rise">
          <div className="relative mx-auto mb-9 grid h-28 w-28 place-items-center rounded-[2rem] bg-secondary text-foreground shadow-[0_10px_0_hsl(16_72%_40%_/_0.2)]"><Sparkles size={42} /><span className="absolute -right-4 -top-3 h-5 w-5 rounded-full bg-accent" /><span className="absolute -bottom-2 -left-4 h-3 w-3 rounded-full bg-primary" /></div>
          <p className="mb-4 text-xs font-bold uppercase tracking-[.2em] text-secondary">Reading trail complete</p>
          <h1 className="display text-[clamp(3rem,8vw,6.5rem)] font-extrabold leading-[.88]">That’s a wrap.<br /><span className="text-primary">You found your thread.</span></h1>
          <p className="mx-auto mt-7 max-w-md text-base leading-relaxed text-muted-foreground">{selectedStories.length > 1 ? <>Your shortlist is ready. Pick the one you want to find first, and keep the others in your back pocket.</> : story ? <><strong className="text-foreground">{story.title}</strong> is waiting when you’re ready. You can carry this feeling with you or start a brand-new trail.</> : 'Your reading trail is complete. A good story is still waiting in the drawer when you’re ready.'}</p>
          {(selectedStories.length > 0 || session.reflection || session.reflectionEmojis.length > 0) && <div className="mx-auto mt-8 max-w-lg rounded-[1.6rem] border border-border bg-card/70 p-5 text-left shadow-sm" data-testid="completion-summary">
            <p className="text-[10px] font-bold uppercase tracking-[.16em] text-secondary">{selectedStories.length > 1 ? 'Your reading shortlist' : 'Your reading trail'}</p>
            {mood && <p className="mt-1 text-xs text-muted-foreground">You wanted something {mood.label.toLowerCase()}.</p>}
            {session.reflection && <blockquote className="mt-4 border-l-4 border-primary/35 pl-4 text-sm italic leading-relaxed text-muted-foreground">“{session.reflection}”</blockquote>}
            {session.reflectionEmojis.length > 0 && <div className="mt-3 text-2xl" aria-label="Your emoji reflection" data-testid="text-reflection-emojis">{session.reflectionEmojis.join(' ')}</div>}
          </div>}
          {selectedStories.length > 0 && <div className="mx-auto mt-5 max-w-lg space-y-5 text-left" data-testid="completion-shortlist">
            {selectedStories.map((item, index) => <section key={item.id} className={`rounded-[1.6rem] border p-4 shadow-sm sm:p-5 ${index === 0 ? 'border-primary/35 bg-primary/5' : 'border-border bg-card/75'}`} data-testid={`completion-book-${item.id}`}>
              <div className="flex items-center gap-4">
                <img src={item.cover} alt={`${item.title} book cover`} className="h-32 w-20 shrink-0 rounded-lg bg-white object-contain shadow-md" />
                <div className="min-w-0">
                  <p className="text-[10px] font-bold uppercase tracking-wider text-secondary">{index === 0 ? 'Find this one first' : 'Also on your shortlist'}</p>
                  <h2 className="mt-1 text-lg font-extrabold leading-tight text-foreground">{item.title}</h2>
                  <p className="mt-1 text-xs text-muted-foreground">by {item.author}</p>
                  <div className="mt-3 rounded-xl bg-background/65 px-3 py-2">
                    <p className="text-[9px] font-bold uppercase tracking-wider text-muted-foreground">Shelf address</p>
                    <p className="mt-0.5 text-sm font-extrabold text-primary">{item.callNumber}</p>
                    <p className="mt-0.5 text-xs font-semibold text-foreground">{item.sublocation}</p>
                  </div>
                </div>
              </div>
              <div className="mt-4 border-t border-border/70 pt-4" data-testid={`completion-shelf-${item.id}`}>
                <ShelfGuide story={item} />
              </div>
            </section>)}
          </div>}
          <div className="mx-auto mt-8 max-w-md rounded-2xl border-2 border-secondary/40 bg-secondary/10 px-5 py-4 text-sm font-bold leading-relaxed text-foreground shadow-sm" data-testid="screenshot-reminder">Take a screenshot so you can come back to this page later.</div>
          {paused ? <div className="mx-auto mt-9 max-w-sm rounded-2xl bg-accent/10 p-5 text-sm font-semibold text-accent animate-rise" data-testid="status-paused"><Pause size={18} className="mx-auto mb-2" /> Your trail is complete. Come back whenever you want another story.</div> : <div className="mt-10 flex flex-wrap justify-center gap-3"><button onClick={startNewMood} className="button-primary inline-flex items-center gap-2 rounded-full bg-primary px-6 py-3.5 text-sm font-bold text-primary-foreground" data-testid="button-new-mood"><RotateCcw size={17} /> Start another trail</button><button onClick={() => setPaused(true)} className="inline-flex items-center gap-2 rounded-full border-2 border-border bg-card px-6 py-3.5 text-sm font-bold text-foreground transition-colors hover:border-primary" data-testid="button-pause"><Pause size={17} /> I’m done for now</button></div>}
          <a href="https://forms.gle/qNYMZBxSJrLCrfJRA" target="_blank" rel="noreferrer" className="mx-auto mt-8 inline-flex items-center gap-2 rounded-full border-b-2 border-secondary px-1 py-2 text-sm font-bold text-foreground transition-colors hover:text-secondary" data-testid="link-feedback-form">Help build Version 2.0 <ExternalLink size={15} /></a>
          <div className="mx-auto mt-14 flex max-w-sm items-center justify-center gap-3 text-xs text-muted-foreground"><Clock3 size={14} /> Your session is saved on this device</div>
        </div>
      </div>
    </main>
  );
}

function DisclaimerPage() {
  const [, setLocation] = useLocation();
  return (
    <main className="page-shell mood-default">
      <Topbar />
      <div className="content-width py-12 sm:py-20">
        <button onClick={() => window.history.length > 1 ? window.history.back() : setLocation('/')} className="button-quiet mb-10 inline-flex items-center gap-2 rounded-full px-3 py-2 text-sm font-bold text-muted-foreground" data-testid="button-back-disclaimer">
          <ArrowLeft size={16} /> Back
        </button>
        <article className="soft-card mx-auto max-w-3xl rounded-[2rem] p-7 sm:p-12 animate-rise">
          <p className="mb-4 text-xs font-bold uppercase tracking-[.2em] text-secondary">A note about the books</p>
          <h1 className="display text-[clamp(2.7rem,7vw,5.4rem)] font-extrabold leading-[.9]">Education, discovery<br /><span className="text-primary">&amp; book love.</span></h1>
          <div className="mt-9 space-y-6 text-base leading-[1.8] text-muted-foreground">
            <p><strong className="text-foreground">Bookhook is an independent educational prototype.</strong> It was created to help readers discover books and support authors, illustrators, and publishers. The creator of Bookhook did not write, illustrate, publish, or own the featured books.</p>
            <p>Book titles, cover art, author names, characters, and quoted passages remain the property of their respective rights holders. Short excerpts are presented for nonprofit educational discussion, criticism, and promotion under fair-use principles. Fair use is evaluated case by case; this notice does not claim ownership or replace legal advice.</p>
            <p>Bookhook does not host full books or chapters. “Read first chapter” links open legitimate third-party preview pages. Availability and preview length are controlled by those providers and may vary by region.</p>
            <p>Please support the people who make these stories by borrowing through a library or purchasing through an authorized bookseller. If you are a rights holder with a concern, use the feedback link so the material can be reviewed promptly.</p>
          </div>
          <div className="mt-10 flex flex-wrap gap-3">
            <button onClick={() => setLocation('/mood')} className="button-primary inline-flex items-center gap-2 rounded-full bg-primary px-6 py-3 text-sm font-bold text-primary-foreground">Find a book <ArrowRight size={17} /></button>
            <a href="https://forms.gle/qNYMZBxSJrLCrfJRA" target="_blank" rel="noopener noreferrer" className="inline-flex items-center gap-2 rounded-full border-2 border-border bg-card px-6 py-3 text-sm font-bold text-foreground hover:border-primary">Contact / feedback <ExternalLink size={15} /></a>
          </div>
        </article>
      </div>
    </main>
  );
}

function Router() {
  const [location] = useLocation();
  useEffect(() => {
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }, [location]);
  return (
    <ErrorBoundary resetKey={location}>
      <Switch>
        <Route path="/" component={Home} />
        <Route path="/mood" component={MoodPage} />
        <Route path="/moments" component={MomentsPage} />
        <Route path="/story/:id" component={StoryPage} />
        <Route path="/reflection" component={ReflectionPage} />
        <Route path="/complete" component={CompletePage} />
        <Route path="/disclaimer" component={DisclaimerPage} />
        <Route component={NotFound} />
      </Switch>
    </ErrorBoundary>
  );
}

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <TooltipProvider>
        <WouterRouter base={import.meta.env.BASE_URL.replace(/\/$/, '')}>
          <AppProvider><Router /></AppProvider>
        </WouterRouter>
        <Toaster />
      </TooltipProvider>
    </QueryClientProvider>
  );
}
/ ---------------------------
export default App;import { type CSSProperties, type ReactNode, createContext, useContext, useEffect, useMemo, useState } from 'react';
/----------------------------------------------------------------------------------------
