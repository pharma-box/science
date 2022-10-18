# makefile

.PHONY: help

.DEFAULT: help

help:
	@echo "---- Help ---------------------"
	@echo "Having Trouble? Make sure you have pip, and python3 installed!"
	@echo "---- Makefile commands --------"
	@echo "install  ....... installs all the required dependencies using pip"
	@echo "start    ....... starts all notebooks"
	@echo "list     ....... list active notebooks"
	@echo "down     ....... shutdown notebooks on 8888"
	@echo "markdown ....... generates markdown files from python notebooks"


install:
	@echo "Installing dependencies... 🎁"
	pip install -r requirements.txt
	@echo "Dependencies installed successfully ✅"

start:
	@echo "Starting Notebooks... ✨"
	jupyter notebook

list:
	jupyter notebook list

down:
	@echo "Shutting down notebooks on 8888.... 👇"
	jupyter notebook stop 8888

markdown:
	jupyter nbconvert ./scrubb-data.ipynb --to markdown --output scrubb-data.md






