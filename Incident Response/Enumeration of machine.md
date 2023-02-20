To enumerate machines after they have been compromised you can do a couple of things, what are those things to do? Here's a list below on what things to check around based on different resources:
- [ ] Check timestamp in the machine
	Modification days are much more granular than meets the eye, you can use this command to check on the date that the machine was compromised and see if you can find something interesting:
	`stat <binary/file/folder>`
	![[Pasted image 20230220002248.png]]
	You can also try `find . -type f -printf "%T+ %pn\n"`
- [ ] Check if any particular file was modified using dataset or touch set using
	You can do this with stat or by verifying the system journal for more precise information.
- [ ] Also checking the history of commands running in the system.