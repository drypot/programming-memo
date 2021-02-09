# Introduction

Programmers are procrastinators. Get in, get some coffee, check the mailbox, read the RSS feeds, read the news, check out latest articles on techie websites, browse through political discussions on the designated sections of the programming forums.

프로그래머들은 굼뱅이들입니다. 출근해서 커피를 마시고, 메일을 확인하고, 구독해둔 RSS 을 읽고, 뉴스를 보고, 기술 웹 사이트의 최신 기사들을 확인하고, 프로그래밍 사이트의 정치 게시판을 훑어봅니다.

Rinse and repeat to make sure nothing is missed. Go to lunch. Come back, stare at the IDE for a few minutes. Check the mailbox. Get some coffee. Before you know it, the day is over.

컵을 닦고는 빠진 것이 없는지 반복해서 확인하고 점심을 먹으러 갑니다. 자리에 돌아오면 몇 분동안 IDE 를 쳐다보다가 메일을 확인하고, 커피를 마십니다. 둘러보니 하루가 끝나있네요.

The only thing, every once in a while challenging articles actually do pop up. If you're looking at the right places you'll find at least one of these every couple of days. These articles are hard to get through and take some time, so they start piling up. Before you know it, you have a list of links and a folder full of PDF files and you wish you had a year in a small hut in the middle of the forest with nobody around for miles so you could catch up. Would be nice if someone came in every morning while you're taking a walk down the river to bring some food and take out the garbage.

사건이라 할만한 유일한 일은 드물게 흥미로운 글을 만나는 것입니다. 적당한 곳을 쳐다보고 있었다면 아마도 며칠에 한번씩은 이런 기사를 발견할 수 있었을 겁니다. 이런 기사들은 읽기 어렵습니다. 시간도 많이 걸립니다. 그러니 걍 쌓이기 시작합니다. =,= 모아 놓은 북마크와 PDF 파일들이 한 가득일 것입니다. 숲속의 작은 오두막에서 아무에게도 방해받지 않고 한 일 년쯤 독서만 할 수 있기를 바라실 겁니다. 매일 아침 강변을 산책하는 동안 누군가가 먹을 것을 가져다 주고 쓰레기를 치워준다면 멋지지 않겠습니까?

I don't know about your list, but a large chunk of the articles in mine are about functional programming. These generally are the hardest to get through. Written in a dry academic language, even the "ten year Wall Street industry veterans" don't understand what functional programming (also referred to as FP) articles are all about. If you ask a project manager in Citi Group or in Deutsche Bank1 why they chose to use JMS instead of Erlang they'll say they can't use academic languages for industrial strength applications. The problem is, some of the most complex systems with the most rigid requirements are written using functional programming elements. Something doesn't add up.

여러분이 무엇을 쌓아뒀는지는 모르겠지만 나는 Functional Programming 에 관한 기사들을 한가득 쌓아두고 있습니다. (Functional Programming 을 앞으로는 줄여서 FP 라고 하겠습니다.) 이것들은 대부분 이해하기에 꽤 벅찬 것들입니다. 건조한 학구적인 문체로 쓰여 있어서 10 년 경력의 월스트리트 IT 베테랑조차 FP 기사들이 도대체 무슨 의미인지 이해하지 못합니다. 시티 그룹이나 도이치 방크의 프로젝트 메니저에게 Erlang 대신 JMS 를 쓴 이유를 뭍는다면 어떻게 아카데믹한 언어를 현업에 쓸 수 있나요라고 말할 겁니다. 문제는, 엄격한 요구사항을 갖는 가장 복잡한 시스템중 일부가 FP 원리를 통해 구현되었다는 겁니다. 이건 먼가 앞뒤가 맞지 않는 사실이군요.

It's true that FP articles and papers are hard to understand, but they don't have to be. The reasons for the knowledge gap are purely historical. There is nothing inherently hard about FP concepts. Consider this article "an accessible guide to FP", a bridge from our imperative minds into the world of FP. Grab a coffee and keep on reading. With any luck your coworkers will start making fun of you for your FP comments in no time.

FP 기사들이나 논문들이 이해하기 어렵다는 것은 맞습니다. 하지만 꼭 그럴 필요가 있는 것은 아닙니다. 이런 지식의 벽이 느껴지는 이유는 순전히 경험적인 (경험부족의) 문제입니다. FP 개념자체에 크게 어려운 것은 없습니다. 이 글을 "바로 통하는 펑셔널 프로그래밍" 정도로 생각하십시오. 절차적 프로그래밍 마인드에서 FP 세계로 다리가 되어드릴 겁니다. 커피잔을 드시고 쭉 읽어가십시오. 잘만 된다면 여러분이 남긴 FP 적인 코멘트에 이게 뭐냐고 깔깔대는 동료들을 발견하는 날이 생각보다 빨리 올 수 있습니다.

So what is FP? How did it come about? Is it edible? If it's as useful as its advocates claim, why isn't it being used more often in the industry? Why is it that only people with PhDs tend to use it? Most importantly, why is it so damn hard to learn? What is all this closure, continuation, currying, lazy evaluation and no side effects business? How can it be used in projects that don't involve a university? Why does it seem to be so different from everything good, and holy, and dear to our imperative hearts?

자, 그럼, FP 가 뭘까요? 어떻게 생겨먹었을까요? 먹을 수는 있을까요? FP 지지자들이 주장하는 것 만큼 FP 가 유용하다면 왜 현업에서 더 자주 사용되지 않는 것일까요? 왜 박사학위 소지자들만 쓰는 것이죠? 가장 중요한 점, 배우는데 왜 그렇게 욕이 나오나요? closure, continuation, currying, lazy evaluation, no side effects 이게 다 뭐하는 것일까요? 학교숙제와 상관 없는 현업 프로젝트에 어떻게 써먹을 수 있을까요? 만족스러우며, 신성하고, 경애해마지 않는, 우리의 이 절차적인 핵심가치들과 왜 그렇게 다르게 보일까요?

We'll clear this up very soon. Let's start with explaining the reasons for the huge gap between the real world and academic articles. The answer is as easy as taking a walk in the park.

이런 의구심들을 곧 다 없애보고자 합니다. 현업와 아카데믹한 글 사이에 큰 차이가 존재하는 이유를 설명하면서 시작하고자 합니다. 답은 공원을 산책하는 것만큼이나 간단합니다.

[Functional Programming For The Rest Of Us](index.md)
