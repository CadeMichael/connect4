<script>
  // get the stylesheet
  import "./app.css";

  // game state
  let win = false;
  // current player
  let player = 1;
 
  /*
   * create the new board and reset game state
   */
  const newBoard = () => {
    win = false; /* reset state */
    return [[0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0]];
  };
 
  // game board 
  let board = newBoard();

  /*
   * Select the column to 'drop' a chip in, see if there is a winner
   */
  const selectMe = (col) => {
    if (win) return false;  
    let rowS = -1;
    let colS = -1;
    if (player === 1)
	  player = 2;
	else
	  player = 1;
	for (let i = 5; i >= 0; i--) {  // start in the last row
	    if (board[i][col] === 0) {  // empty entry in col
	      board[i][col] = player;   // fill
	      rowS = i; 
	      colS = col;
	      i = -10;                  // break out of loop
	    }
	}
    if (rowS >= 0 && colS >= 0) {       // rowS and colS have been set
      win = (winH(rowS, player) || winV(colS, player) || winDU(colS, rowS, player) || winDD(colS, rowS, player));
    }
  };

  /*
   * determine if there is a winner in a row
   */
  const winH = (rowNum, player) => {
    let tot = 0;
    for(let x = 0; x < 7; x++) {
      if (board[rowNum][x] === player) {
	tot ++;
	if (tot === 4){
	  tot = 0;
	  return true;
	}
      } else {
	tot = 0;
      }
    }
    return false;
  };

  /*
   * determine if there is a winner in a column
   */
  const winV = (colNum, player) => {
    let vtot = 0;
    for(let y = 0; y < 6; y++) {
      if (board[y][colNum] === player) {
	vtot ++;
	if (vtot === 4){
	  vtot = 0;
	  return true;
	}
      } else {
	vtot = 0;
      }
    }
    return false;
  };

  /*
   * determine if there is a winner in the diagonal up 
   */
  const winDU = (colNum, rowNum, player) => {
    let totDU = 0;
    let colDU = colNum;
    let rowDU = rowNum;
    while (colDU > 0 && rowDU < 5) {
      colDU--;
      rowDU++;
    }
    while (rowDU >= 0 && colDU <= 6) {
      if (board[rowDU][colDU] === player) {
	totDU++;
	if (totDU === 4) return true;
      }
      else totDU = 0;
      rowDU--;
      colDU++;
    }
    return false;
  }

  /*
   * determine if there is a winner in the diagonal down
   */
  const winDD = (colNum, rowNum, player) => {
    let totDD = 0;
    let colDD = colNum;
    let rowDD = rowNum;
    while (colDD < 6 && rowDD < 5) {
      colDD++;
      rowDD++;
    }
    while (rowDD >= 0 && colDD >= 0) { // need 'or eq'
      if (board[rowDD][colDD] === player) {
	totDD++;
	if (totDD === 4) return true;
      }
      else totDD = 0;
      rowDD--;
      colDD--;
    }
    return false;
  }
</script>

<main>
    <div class="foreground">
	<div class="title">
	    <h1>Connect 4</h1>
	</div>
	<!-- if winner, show banner -->
	{#if win}
	  <div class="victory">
	    {#if player === 1}
	      <div class="w1">
		<h2>We Have a Winner!!!</h2>
	      </div>
	    {:else}
	      <div class="w2">
		<h2>We Have a Winner!!!</h2>
	      </div>
	    {/if}
	  </div>
	{/if}
	<!-- Create the buttons that determine column to drop chip -->
	<table class="selects">
	   <tr>
	    {#each board[0] as column, i}
	      <td>
		<!-- conditional logic to determine color of chip -->
		{#if player === 1}
		    <button class="sel1" on:click={() => {
						  selectMe(i);
						  /* play against comp */
						  selectMe(Math.floor(Math.random() * 7));
						  }}>
		    &#8595; 
		  </button>
		{:else}
		  <button class="sel2" on:click={() => {
						selectMe(i);
						/* play against comp */
						selectMe(Math.floor(Math.random() * 7));
						}}>
		    &#8595; 
		  </button>
		{/if}
	      </td>
	    {/each}
	  </tr>
	</table>
	<table class="board">
	  <tbody>
	      <!-- instantiate the table "slots" based on corresponding board value -->
	      {#each board as row}
		<tr>
		  {#each row as i}
		    {#if i === 0}
		      <td class="slot"></td>
		    {:else if i === 1}
		      <td class="takenP1"></td>
		  {:else}
		    <td class="takenP2"></td>
		  {/if}
		{/each}
	      </tr> 
	    {/each}
	  </tbody>
	</table>
	<!-- reset the game state -->
	<button on:click={() => {
	  board = newBoard();
	  player = 1;
	  }} class="reset">
	  new game!
	</button>
    </div>
</main>

