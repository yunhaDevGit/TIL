# setter 메서드로 값을 설정해주었는데, update가 안되는 문제


	@Service  
	public class PostServiceImpl implements PostService {
		...
		@Override  
		public Post savePost(Post post) {  
			Board board = boardService.getBoardByCategory(post.getBoard().getCategory());  
			board.setPostCount(board.getPostCount()+1);  
			post.setBoard(board);  
			return postRepository.save(post);  
		}
	}
		
service Layer를 다음과 같이 작성했을 때, post는 제대로 DB에 잘 저장이 되었는데 board의 postCount 값이 update가 되지 않았다.
Spring Data JPA를 사용하면 setter에 값을 설정해주면 자동으로 update query를 날려준다고 했는데 왜 안되는지 한참을 헤맸다.

이유는 Service Layer에 @Transactional 설정을 해주지 않아서였다.

    @Transactional  
	@Override  
	public Post savePost(Post post) {  
		  Board board = boardService.getBoardByCategory(post.getBoard().getCategory());  
		  board.setPostCount(board.getPostCount()+1);  
		  post.setBoard(board);  
		  
		 return postRepository.save(post);  
	}

@Transactional 어노테이션을 붙여주니 정상적으로 동작하였다.

사실 @Transactional 어노테이션이 없어서 안되는 건 줄 모르고, 계속해서 'spring data jpa update setter' 이런식으로 구글링을 했다.
그런데 실장님이 지나가다가 뭐하냐고 물어보셔서 말씀드렸더니...ㅎ바로 해결^^
@Transactional을 언제?왜? 사용해야 하는지 아직 몰라서 사용을 못했는데, 이번 기회에 알아보자!


#### 해결 이유
간단하게 해결된 이유를 먼저 말하자면, JPA 더티 체킹(dirty checking) 기능 때문이다.
우선 더티 체킹이 뭔지부터 알아야 한다.

**더티 체킹이란?**

- JPA를 사용하면서 엔티티 매니저가 엔티티를 저장/조회/수정/삭제를 하는데, 이때 수정에 대한 메서드가 따로 없다. 
더티 체킹은 Transaction 안에서 엔티티의 변경이 일어나면, 변경 내용을 자동으로 데이터베이스에 반영하는 JPA 특징이다. 

- 트랜잭션 커밋 이후, 트랜잭션이 끝나는 시점에 모든 엔티티에 대한 정보를 DB에 반영한다.



### @Transactional
스프링에서 트랜잭션 처리를 지원하는 방법 중 하나
- transaction `begin`, `commit`을 자동으로 수행해준다
- 예외를 발생 시키면, `rollback` 처리를 자동으로 해준다

JPA의 객체 변경 감지는 transaction이 commit 될 때, 작동한다.
