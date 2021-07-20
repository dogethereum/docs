all: battle-states.svg battle-states-legend.svg superblock-claims.svg battle-states.png battle-states-legend.png superblock-claims.png

.PHONY: show-battle-states

battle-states.png: battle-states.dot
	dot -Tpng $< -o $@

battle-states.svg: battle-states.dot
	dot -Tsvg $< -o $@

battle-states-legend.png: battle-states-legend.dot
	dot -Tpng $< -o $@

battle-states-legend.svg: battle-states-legend.dot
	dot -Tsvg $< -o $@

superblock-claims.png: superblock-claims.dot
	dot -Tpng $< -o $@

superblock-claims.svg: superblock-claims.dot
	dot -Tsvg $< -o $@


# These rules are shortcuts to rapidly visualize the graphs
# It requires the image viewer sxiv
show-battle-states: battle-states.png battle-states-legend.png
	sxiv $^ &

show-superblock-claims: superblock-claims.png
	sxiv $^ &
