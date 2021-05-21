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
