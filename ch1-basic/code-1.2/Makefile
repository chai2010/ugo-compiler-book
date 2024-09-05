default:
	wa run main.wa > _main.ll
	clang -Wno-override-module -o a.out _main.ll
	./a.out || echo $$?

	-@rm a.out

clean:
	-rm a.out
