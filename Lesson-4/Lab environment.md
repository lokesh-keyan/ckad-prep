Running your first application
	source <(kubectl completion bash)
	kubectl completion bash > ~/.kube/completion.bash.inc
	
	Below what its doing is, taking the completion.bash and putting it in .bash_profile so that completion happens on start
	printf "
  # kubectl shell completion
  source '$HOME/.kube/completion.bash.inc'
  " >> $HOME/.bash_profile
  
  
  kubectl run -h | less
