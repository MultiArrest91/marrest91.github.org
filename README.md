<!DOCTYPE html>
<html>
<body>
<button onclick="startstop()">Pause/Resume</button>
<button onclick="resetTo0()">Reset to 0</button>
<button onclick="goToMaxValue()">Go to maximum value</button>
<button onclick="showNumber()">Show number</button>
Max number of digits before switching to scientific (min: 10, max: 308): <input type="text" value="21" id="maxDigits"></input> Reduce fps:<input type="checkbox" id="fps20"></input>
<br>
<button onclick="plus1s()">Fast forward 1 second</button>
<button onclick="minus1s()">Rewind 1 second</button>
<button onclick="plus1min()">Fast forward 1 minute</button>
<button onclick="minus1min()">Rewind 1 minute</button>
<button onclick="plus1h()">Fast forward 1 hour</button>
<button onclick="minus1h()">Rewind 1 hour</button>
<button onclick="speedDouble()">Speed x2</button>
<button onclick="speedHalf()">Speed /2</button>
<button onclick="reverseSpeed()">Reverse</button>
<br>
Speed multiplier: <input type="text" value="1" id="speedMult"></input>
<p> </p>
<p><center><font face="arial" size="6" id="number2"></font></center></p>
<p><font face="arial" size="6" id="number"></font></p>
<script>
var t = 0;
var speedMultiplier = 1;
var numberDisplay = 0;

function save() {
	localStorage.setItem('numsv2', JSON.stringify(t));
}

function load() {
	if (localStorage.getItem('numsv2')) {
		t = JSON.parse(localStorage.getItem('numsv2'));
	}
}

function tier0number(num) { // number from 0 to 999
	var ones = ["","one","two","three","four","five","six","seven","eight","nine"];
	var teens = ["ten","eleven","twelve","thirteen","fourteen","fifteen","sixteen","seventeen","eighteen","nineteen"];
	var tens = ["","ten","twenty","thirty","forty","fifty","sixty","seventy","eighty","ninety"];
	var numberName = "";
	num = Math.floor(num);
	if (num > 999 || num < 0) {
		return "";
	}
	if (num >= 100) {
		numberName += ones[Math.floor(num/100)] + " hundred ";
	}
	if (num%100 >= 20) {
		numberName += tens[Math.floor((num%100)/10)];
		if (num%10 != 0) {
			numberName += "-" + ones[num%10];
		}
	} else if (num%100 >= 10) {
		numberName += teens[num%10];
	} else if (num != 0) {
		numberName += ones[num%10];
	} else {
		numberName = "zero";
	}
	
	return numberName.trim();
}
function tier1illion(num) { // number from 0 to 999, 1=million, 2=billion, etc.
	var ones = ["","m","b","tr","quadr","quint","sext","sept","oct","non"];
	var ones2 = ["","un","duo","tre","quattuor","quin","sex","septen","octo","novem"];
	var tens = ["","dec","vigint","trigint","quadragint","quinquagint","sexagint","septuagint","octogint","nonagint"];
	var hundreds = ["","cent","ducent","trecent","quadringent","quingent","sescent","septingent","octingent","nongent"];
	var numberName = "";
	num = Math.floor(num);
	if (num > 999 || num < 0) {
		return "";
	}
	if (num >= 100) {
		if (num%100 >= 10) {
			numberName += ones2[num%10] + tens[Math.floor((num%100)/10)] + "i" + hundreds[Math.floor(num/100)];
		} else if (num == 103) {
			numberName = "trescent";
		} else {
			numberName += ones2[num%10] + hundreds[Math.floor(num/100)];
		}
	} else if (num >= 10) {
		numberName += ones2[num%10] + tens[Math.floor((num%100)/10)];
	} else {
		numberName += ones[num];
	}
	if (num == 0) {
		return "thousand";
	} else {
		return numberName + "illion";
	}
}
function tier1illionPrefix(num, vers) { // illion from 0 to 999, vers=0, use m,b,t for 1,2,3, vers=1 use un,duo,tre, vers=2 ignore "un" like so we have micrillion instead of unmicrillion
	var ones = ["","m","b","tr","quadr","quint","sext","sept","oct","non"];
	var ones2 = ["","un","duo","tre","quattuor","quin","sex","septen","octo","novem"];
	var tens = ["","dec","vigint","trigint","quadragint","quinquagint","sexagint","septuagint","octogint","nonagint"];
	var hundreds = ["","cent","ducent","trecent","quadringent","quingent","sescent","septingent","octingent","nongent"];
	var numberName = "";
	num = Math.floor(num);
	if (num > 999 || num < 0) {
		return "";
	}
	if (num >= 100) {
		if (num%100 >= 10) {
			numberName += ones2[num%10] + tens[Math.floor((num%100)/10)] + "i" + hundreds[Math.floor(num/100)];
		} else if (num == 103) {
			numberName = "trescent";
		} else {
			numberName += ones2[num%10] + hundreds[Math.floor(num/100)];
		}
		if (vers == 2) {numberName += "i"}
	} else if (num >= 10) {
		numberName += ones2[num%10] + tens[Math.floor((num%100)/10)];
		if (vers == 2) {numberName += "i"}
	} else if (vers == 2 && num == 1) {
		return "";
	} else if (vers > 0) {
		numberName += ones2[num];
	} else {
		numberName += ones[num];
	}
	return numberName;	
}
function tier1number(logNum) {
	var numberName = "";
	var num = 0;
	if (logNum >= 3003) {
		return "";
	}
	if (logNum < 15) {
		num = Math.floor(10**logNum);
		numArray = [num%1000, Math.floor((num/1000)%1000), Math.floor((num/1e6)%1000), Math.floor((num/1e9)%1000), Math.floor((num/1e12)%1000)];
		if (numArray[4] > 0) {numberName += tier0number(numArray[4]) + " trillion<br>"}
		if (numArray[3] > 0) {numberName += tier0number(numArray[3]) + " billion<br>"}
		if (numArray[2] > 0) {numberName += tier0number(numArray[2]) + " million<br>"}
		if (numArray[1] > 0) {numberName += tier0number(numArray[1]) + " thousand<br>"}
		if (numArray[0] > 0) {numberName += tier0number(numArray[0])}
	} else {
		num = Math.floor(10**(logNum%3 + 9));
		numArray = [num%1000, Math.floor((num/1000)%1000), Math.floor((num/1e6)%1000), Math.floor((num/1e9)%1000)]
		if (logNum < 303) {
			for (i=3; i>=1; i--) {
				if (numArray[i] > 0) {numberName += tier0number(numArray[i]) + " " + tier1illion(Math.floor((logNum-12)/3+i)) + "<br>"}
			}
		} else {
			for (i=3; i>=2; i--) {
				if (numArray[i] > 0) {numberName += tier0number(numArray[i]) + "<br>" + tier1illion(Math.floor((logNum-12)/3+i)) + "<br>"}
			}
		}
	}
	return numberName.trim();
}
function tier2illionPrefix(num, vers) {
// vers = 0: right before illion, 1: tier-1 separator, 2: tier-2 separator
	var ones = ["","milli","micro","nano","pico","femto","atto","zepto","yocto","xono"];
	var ones2 = ["","mill","micr","nan","pic","femt","att","zept","yoct","xon"];
	var ones3 = ["","me","due","trio","tetre","pente","hexe","hepte","octe","enne"];
	var teens = ["vec","mec","duec","trec","tetrec","pentec","hexec","heptec","octec","ennec"];
	var tens = ["","","icos","triacont","tetracont","pentacont","hexacont","heptacont","octacont","ennacont"];
	var hundreds = ["","hect","dohect","triahect","tetrahect","pentahect","hexahect","heptahect","octahect","ennahect"];
	var numberName = "";
	num = Math.floor(num);
	if (num > 999 || num < 0) {
		return "";
	}
	if (num >= 100) {
		if (Math.floor((num/10)%10) == 1) {
			numberName += teens[num%10] + "e" + hundreds[Math.floor(num/100)];
		} else if (Math.floor((num/10)%10) == 0) {
			numberName += ones3[num%10] + hundreds[Math.floor(num/100)];
		} else {
			numberName += ones3[num%10] + tens[Math.floor((num/10)%10)] + "e" + hundreds[Math.floor(num/100)];
		}
		if (vers == 1) {numberName += "o"};
		if (vers == 2) {numberName += "e"};
	} else if (num >= 20) {
		numberName += ones3[num%10] + tens[Math.floor((num/10)%10)];
		if (vers == 1) {numberName += "o"};
		if (vers == 2) {numberName += "e"};
	} else if (num >= 10) {
		numberName += teens[num%10];
		if (vers == 1) {numberName += "o"};
		if (vers == 2) {numberName += "e"};
	} else {
		if (vers == 0) {
			numberName += ones2[num];
		} else if (vers == 1) {
			numberName += ones[num];
		} else if (vers == 2 && num > 1) {
			numberName += ones2[num] + "e";
		}
	}
	return numberName.trim();
}
function tier2number(loglogNum) {
	var numberName = "";
	var logIllionNumber = 0;
	var logNum = 0;
	if (loglogNum < 20) {
		logIllionNumber = Math.log10(10**loglogNum/3-1);
	} else {
		logIllionNumber = loglogNum - Math.log10(3);	
	}
	if (logIllionNumber >= 3000) {
		return "";
	}
	if (logIllionNumber < 12) {
		var illionNumber = Math.floor(10**logIllionNumber);
		logNum = 3*10**logIllionNumber+3;
		numArray = [Math.floor((10**(logNum%3)*1000)%1000), Math.floor(10**(logNum%3))];
		if (illionNumber >= 1e4) {k=0} else {k=1};
		for (i=0; i<=k; i++) {
		illionArray = [illionNumber%1000, Math.floor((illionNumber/1000)%1000), Math.floor((illionNumber/1e6)%1000), Math.floor((illionNumber/1e9)%1000)];
		numberName += tier0number(numArray[1-i]) + "<br>";
		if (illionArray[3] > 0) {
			if (illionNumber%1e9 == 0) {
				numberName += tier1illionPrefix(illionArray[3], 2) + "nan".fontcolor("blue");
			} else {
				numberName += tier1illionPrefix(illionArray[3], 2) + "nano".fontcolor("blue") + "-";
			}
		}
		if (illionArray[2] > 0) {
			if (illionNumber%1e6 == 0) {
				numberName += tier1illionPrefix(illionArray[2], 2) + "micr".fontcolor("blue");
			} else {
				numberName += tier1illionPrefix(illionArray[2], 2) + "micro".fontcolor("blue") + "-";
			}
		}
		if (illionArray[1] > 0) {
			if (illionNumber%1e3 == 0) {
				numberName += tier1illionPrefix(illionArray[1], 2) + "mill".fontcolor("blue");
			} else {
				numberName += tier1illionPrefix(illionArray[1], 2) + "milli".fontcolor("blue") + "-";
			}
		}
		if (illionArray[0] > 0) {
			numberName += tier1illionPrefix(illionArray[0], 0);
		}
		numberName += "illion<br>";
		illionNumber--;
		}
	} else { // illion number > 1B
		numberName += "one ";
		highestTier2illion = Math.floor(logIllionNumber/3);
		numArray = [Math.floor(10**(logIllionNumber%3+6)%1000), Math.floor(10**(logIllionNumber%3+3)%1000), Math.floor(10**(logIllionNumber%3))];
		if (numArray[0] == 0 && numArray[1] == 0) {
			numberName += tier1illionPrefix(numArray[2], 2) + tier2illionPrefix(highestTier2illion, 0).fontcolor("blue");
		} else {
			numberName += tier1illionPrefix(numArray[2], 2) + tier2illionPrefix(highestTier2illion, 1).fontcolor("blue") + "-";
		}
		if (numArray[1] != 0) {
			if (numArray[0] == 0) {
				numberName += tier1illionPrefix(numArray[1], 2) + tier2illionPrefix(highestTier2illion-1, 0).fontcolor("blue");
			} else {
				numberName += tier1illionPrefix(numArray[1], 2) + tier2illionPrefix(highestTier2illion-1, 1).fontcolor("blue") + "-";
			}
		}
		if (numArray[0] != 0) {
			numberName += tier1illionPrefix(numArray[0], 2) + tier2illionPrefix(highestTier2illion-2, 0).fontcolor("blue");
		}
		numberName += "illion";
	}
	return numberName.trim();
}
function tier3illionPrefix(num, vers) {
// vers = 0: right before illion, 1: tier-2 separator, 2: tier-3 separator, 3: after kal
	var ones = ["","kill","meg","gig","ter","pet","ex","zett","yott","xenn"];
	var ones2 = ["k","ken","cod","ctr","cter","cpet","kect","czet","kyot","cxen"]; // for ik-, trak-, tek-
	var ones3 = ["t","ten","tod","tr","ter","pet","tect","zet","yot","xen"]; // for ho-, bo-
	var ones4 = ["","","d","tr","t","p","ex","z","y","n"] // for kal-, mej-, gij
	var ones5 = ["","en","od","tr","ter","pet","ect","zet","yot","xen"]; // after kal-
	var teens = ["dak","hend","dok","tradak","tedak","pedak","exdak","zedak","yodak","nedak"];
	var teens2 = ["dak","tend","dok","tradak","tedak","pedak","texdak","zedak","yodak","nedak"]; // for ho-, bo-
	var tens = ["","","i","tra","te","pe","exa","za","yo","ne"];
	var tens2 = ["","","ti","tra","te","pe","texa","za","yo","ne"]; // for ho-, bo-
	var hundreds = ["","ho","bo","tro","to","po","exo","zo","yoo","no"];
	var numberName = "";
	num = Math.floor(num);
	if (num > 999 || num <= 0) {
		return "";
	}
	if (num >= 100) {
		if (num%100 >= 20) {
			numberName += hundreds[Math.floor(num/100)] + tens2[Math.floor((num/10)%10)] + ones2[num%10];
		} else if (num%100 >= 10) {
			numberName += hundreds[Math.floor(num/100)] + teens2[num%10];
		} else {
			numberName += hundreds[Math.floor(num/100)] + ones3[num%10];
		}
	} else if (num >= 20) {
		numberName += tens[Math.floor((num/10)%10)] + ones2[num%10];
	} else if (num >= 10) {
		numberName += teens[num%10];
	} else if (vers == 3) {
		numberName += ones5[num%10];
	} else if (vers == 2) {
		numberName += ones4[num%10];
	} else {
		numberName += ones[num%10];
	}
	if (vers == 1) {
		numberName += "a";
	}
	return numberName.trim();
}
function tier3number(logloglogNum) {
// 1e(3e3000) -> log(3000.4771212547197)
// killillion -> 3.477190319603795
	var numberName = "one\u00A0";
	var num = 0;
	var illionNumber = 0;
	var logNum = 0;
	if (logloglogNum < 15) {
		logloglogNum = Math.log10(10**(logloglogNum)-Math.log10(3));
	}
	var tier2illion = Math.floor(10**logloglogNum/3); // 1000 = killillion, 1m = meg-
	if (tier2illion < 1e9) {
		numArray = [Math.floor((10**(10**logloglogNum%3)*1000)%1000), Math.floor(10**(10**logloglogNum%3))];
		if (tier2illion < 1e4 && numArray[0] != 0) {k=1} else {k=0};
		for (i=0; i<=k; i++) {
			illionArray = [Math.floor(tier2illion%1000), Math.floor((tier2illion/1000)%1000), Math.floor((tier2illion/1e6)%1000)]; // ones, thousands, millions
			if (tier2illion < 1e9) {
				numberName += tier1illionPrefix(Math.floor(numArray[1-i]), 2); // T1 prefix
				if (tier2illion >= 1e3) {
					if (Math.floor(numArray[1-i]) > 1) {numberName += "-"};
					numberName += "<br>"
				}
			}
			if (illionArray[2] > 0) {
				if (illionArray[1] > 0 || illionArray[0] > 0) {
					numberName += tier2illionPrefix(illionArray[2], 2).fontcolor("blue") + "mega".fontcolor("green");
				} else if (i!=k) {
					numberName += tier2illionPrefix(illionArray[2], 2).fontcolor("blue") + "mego".fontcolor("green");
				} else {
					numberName += tier2illionPrefix(illionArray[2], 2).fontcolor("blue") + "meg".fontcolor("green");
				}
			}
			if (illionArray[1] > 0) {
				if (illionArray[0] > 0) {
					numberName += tier2illionPrefix(illionArray[1], 2).fontcolor("blue") + "killa".fontcolor("green");
				} else if (i!=k) {
					numberName += tier2illionPrefix(illionArray[1], 2).fontcolor("blue") + "killo".fontcolor("green");
				} else {
					numberName += tier2illionPrefix(illionArray[1], 2).fontcolor("blue") + "kill".fontcolor("green");
				}
			}
			if (illionArray[0] > 0) {
				if (i != k) {
					numberName += tier2illionPrefix(illionArray[0], 1).fontcolor("blue");
				} else {
					numberName += tier2illionPrefix(illionArray[0], 0).fontcolor("blue");
				}
			}
			if (numArray[0] != 0 && i != k) {
				numberName += "-<br>";
			}
			tier2illion--;
		}
		numberName.replace("<br><br>","<br>");
	} else {
		var tier3illion = logloglogNum-Math.log10(3); // 9 = gigillion
		if (tier3illion >= 3000) {
			return "";
		}
		numArray = [Math.floor(10**(tier3illion%3+6)%1000), Math.floor(10**(tier3illion%3+3)%1000), Math.floor(10**(tier3illion%3))];
		illionArray = [Math.floor(tier3illion/3-2), Math.floor(tier3illion/3-1), Math.floor(tier3illion/3)];
		if (numArray[0] == 0 && numArray[1] == 0) {
			numberName += tier2illionPrefix(numArray[2], 2).fontcolor("blue") + tier3illionPrefix(illionArray[2], 0).fontcolor("green");
		} else {
			numberName += tier2illionPrefix(numArray[2], 2).fontcolor("blue") + tier3illionPrefix(illionArray[2], 1).fontcolor("green");
		}
		if (numArray[1] != 0) {
			if (numArray[0] == 0) {
				numberName += tier2illionPrefix(numArray[1], 2).fontcolor("blue") + tier3illionPrefix(illionArray[1], 0).fontcolor("green");
			} else {
				numberName += tier2illionPrefix(numArray[1], 2).fontcolor("blue") + tier3illionPrefix(illionArray[1], 1).fontcolor("green");
			}
		}
		if (numArray[0] != 0) {
			numberName += tier2illionPrefix(numArray[0], 2).fontcolor("blue") + tier3illionPrefix(illionArray[0], 0).fontcolor("green");
		}
	}
	return (numberName+"illion").trim();
}
function tier4illionPrefix(num, vers) {
// vers = 0: right before illion, 1: tier-2 separator, 2: tier-3 separator
	var nums = ["","kal","mej","gij","ast","lun","ferm","jov","sol","bet","gloc","gax","sup","vers","mult","pyr","gunt","kentr","onl","paptr","hous","hors"];
	var nums2 = ["","al","ej","ij","ast","un","erm","ov","ol","et","loc","ax","up","ers","ult","yr","unt","entr","onl","aptr","ous"]; // using loc instead of oc because of the sequence gloc,doc,troc,toc,poc can cause confusion with meg,gig,ter,pet with pet having two possible meanings.
	var numberName = "";
	num = Math.floor(num);
	if (num > 20 || num <= 0) {
		return "";
	}
	if (vers == 0) {
		return nums[num];
	} else if (vers == 1) {
		return nums2[num];
	}
}
function tier4number(loglogloglogNum) {
	var numberName = "one ";
	var num = 0;
	var illionNumber = 0;
	var logNum = 0;
	if (loglogloglogNum < 15) {
		loglogloglogNum = Math.log10(10**(loglogloglogNum)-Math.log10(3));
	}
	var tier3illion = Math.floor(10**loglogloglogNum/3); // 1000 = kalillion, 1m = mej-
	if (tier3illion < 1e12) {
		numArray = [Math.floor((10**(10**loglogloglogNum%3)*1000)%1000), Math.floor(10**(10**loglogloglogNum%3))];
		if (tier3illion < 1e5 && numArray[0] != 0) {k=1} else {k=0};
		for (i=0; i<=k; i++) {
			illionArray = [Math.floor(tier3illion%1000), Math.floor((tier3illion/1000)%1000), Math.floor((tier3illion/1e6)%1000), Math.floor(tier3illion/1e9)]; // ones, thousands, millions
			if (tier3illion < 1e12) {
				numberName += tier2illionPrefix(numArray[1-i], 2).fontcolor("blue"); // T2 prefix
				if (numArray[1-i] > 1) {numberName += "-"};
				numberName += "<br>";
			}
			if (illionArray[3] > 0) {
				if (illionArray[3] > 1) {
					numberName += tier3illionPrefix(illionArray[3], 2).fontcolor("green") + "ij".fontcolor("red");
				} else {
					numberName += "gij".fontcolor("red");
				}
				if (illionArray[1] > 0 || illionArray[2] > 0) {
					numberName += "i".fontcolor("red");
				}
			}
			if (illionArray[2] > 0) {
				if (illionArray[2] > 1) {
					numberName += tier3illionPrefix(illionArray[2], 2).fontcolor("green") + "ej".fontcolor("red");
				} else {
					numberName += "mej".fontcolor("red");
				}
				if (illionArray[1] > 0) {
					numberName += "i".fontcolor("red");
				}
			}
			if (illionArray[1] > 0) {
				if (illionArray[1] > 1) {
					numberName += tier3illionPrefix(illionArray[1], 2).fontcolor("green") + "al".fontcolor("red");
				} else {
					numberName += "kal".fontcolor("red");
				}
			}
			if (illionArray[0] > 0) {
				numberName += tier3illionPrefix(illionArray[0], 3).fontcolor("green");
			}
			if (i != k && numArray[0] > 0) {
				numberName += "a-<br>".fontcolor("green");
			}
			tier3illion--;
		}
	} else if (tier3illion < 1e63) {
		numArray = [Math.floor(10**(Math.log10(tier3illion)%3+6)%1000), Math.floor(10**(Math.log10(tier3illion)%3+3)%1000), Math.floor(10**(Math.log10(tier3illion)%3)%1000)];
		illionArray = [Math.floor(Math.log10(tier3illion)/3)-2, Math.floor(Math.log10(tier3illion)/3)-1, Math.floor(Math.log10(tier3illion)/3)];
		if (numArray[2] > 0) {
			if (numArray[2] > 1) {
				numberName += tier3illionPrefix(numArray[2], 2).fontcolor("green") + tier4illionPrefix(illionArray[2], 1).fontcolor("red");
			} else {
				numberName += tier4illionPrefix(illionArray[2], 0).fontcolor("red");
			}
			if (numArray[1] > 0 || numArray[0] > 0) {
				numberName += "i".fontcolor("red");
			}
		}
		if (numArray[1] > 0) {
			if (numArray[1] > 1) {
				numberName += tier3illionPrefix(numArray[1], 2).fontcolor("green") + tier4illionPrefix(illionArray[1], 1).fontcolor("red");
			} else {
				numberName += tier4illionPrefix(illionArray[1], 0).fontcolor("red");
			}
			if (numArray[0] > 0) {
				numberName += "i".fontcolor("red");
			}
		}
		if (numArray[0] > 0) {
			if (numArray[0] > 1) {
				numberName += tier3illionPrefix(numArray[0], 2).fontcolor("green") + tier4illionPrefix(illionArray[0], 1).fontcolor("red");
			} else {
				numberName += tier4illionPrefix(illionArray[0], 0).fontcolor("red");
			}
		}
	}
	return (numberName+"illion").trim();
}
function valueNumber(t) {
	var x = 10*(t+1e4)**0.75-1e4+t**1.1/100;
	if (document.getElementById("fps20").checked) {
		x = 10*((t-t%(5*speedMultiplier))+1e4)**0.75-1e4+(t-t%(5*speedMultiplier))**1.1/100;
	}
	if (x < 30000) {
		return tier0number((x/1000)**2 + x/300); // up to 10^3
	} else if (x < 300000) {
		i = (x-30000)/270000
		return tier1number(3 + 7*i); // up to 10^10
	} else if (x < 2300000) {
		//i = Math.min((x-300000)/2000000,Math.log10(Math.log10(12)))
		i = (x-300000)/2000000
		if (10**10**i < 3003) {
			return tier1number(10**10**i);
		} else {
			return tier2number(10**i);
		} // from 10^10 to 10^^3
	} else if (x < 5000000) {
		i = (x-2300000)/2700000
		if (10**10**i < 3000.47712125471966) {
			return tier2number(10**10**i);
		} else {
			return tier3number(10**i);
		} // from 10^^3 to 10^^4
	} else if (x < 10000000) {
		i = (x-5000000)/5000000
		if (10**10**i < 3000.47712125471966) {
			return tier3number(10**10**i);
		} else {
			return tier4number(10**i);
		} // from 10^^4 to 10^^5
	} else {
		i = (x-10000000)/7000000
		if (10**10**i < 45.477121254719662) {
			return tier4number(10**10**i);
		}
	}
	return "one " + "nonecxen".fontcolor("green") + "ousi".fontcolor("red") + "nonecxen".fontcolor("green") + "aptri".fontcolor("red") + "nonecxen".fontcolor("green") + "onl".fontcolor("red") + "illion";
}
function valueNumber2(t) {
	var numDigits = document.getElementById("maxDigits").value;
	if (isNaN(numDigits)) {numDigits = 10}
	numDigits = Math.max(Math.min(Math.floor(numDigits),308),10);
	var x = 10*(t+1e4)**0.75-1e4+t**1.1/100;
	var getFullNumber = "";
	if (document.getElementById("fps20").checked) {
		x = 10*((t-t%(5*speedMultiplier))+1e4)**0.75-1e4+(t-t%(5*speedMultiplier))**1.1/100;
	}
	if (x < 30000) {
		return Math.floor((x/1000)**2 + x/300).toLocaleString(); // up to 10^3
	} else if (x < 300000) {
		i = (x-30000)/270000
		return Math.floor(10**(3 + 7*i)).toLocaleString(); // up to 10^10
	} else if (x < 2300000) {
		//i = Math.min((x-300000)/2000000,Math.log10(Math.log10(12)))
		i = (x-300000)/2000000
		if (10**10**i < numDigits) {
			return Math.floor(10**10**10**i).toLocaleString();
		} else {
			if (10**10**i < 100) {
				getFullNumber = "<br><font size=\"4\">" + Math.floor(10**10**10**i).toLocaleString() + "</font>";
			}
			return (Math.floor(10**((10**10**i)%1)*1000000)/1000000).toFixed(6) + " × 10<sup>" + Math.floor(10**10**i).toLocaleString() + "</sup>" + getFullNumber;
		} // from 10^10 to 10^^3
	} else if (x < 5000000) {
		i = (x-2300000)/2700000
		if (10**10**i < numDigits) {
			return "10<sup>" + Math.floor(10**10**10**i).toLocaleString() + "</sup>";
		} else {
			return "10<sup>" + (Math.floor(10**((10**10**i)%1)*1000000)/1000000).toFixed(6) + " × 10<sup>" + Math.floor(10**10**i).toLocaleString() + "</sup></sup>";
		} // from 10^^3 to 10^^4
	} else if (x < 10000000) {
		i = (x-5000000)/5000000
		if (10**10**i < numDigits) {
			return "10<sup>10<sup>" + Math.floor(10**10**10**i).toLocaleString() + "</sup></sup>";
		} else {
			return "10<sup>10<sup>" + (Math.floor(10**((10**10**i)%1)*1000000)/1000000).toFixed(6) + " × 10<sup>" + Math.floor(10**10**i).toLocaleString() + "</sup></sup></sup>";
		} // from 10^^4 to 10^^5
	} else {
		i = (x-10000000)/7000000
		if (10**10**i < numDigits) {
			return "10<sup>10<sup>10<sup>" + Math.floor(Math.min(10**10**10**i, 3e45)).toLocaleString() + "</sup></sup></sup>";
		} else {
			imin = Math.min(10**10**i, 45.477121254719662);
			return "10<sup>10<sup>10<sup>" + (Math.floor(10**(imin%1)*1000000)/1000000).toFixed(6) + " × 10<sup>" + Math.floor(imin).toLocaleString() + "</sup></sup></sup></sup>";
		}
	}
	return "";
}
var showNum = true;
var runInterval = 16.666; // milliseconds
var tAdder = runInterval*0.1;
function startstop() {
if (tAdder == 0) {
	tAdder = runInterval*0.1;
	} else {
	tAdder = 0;
	}
}
function resetTo0() {t = 0}
function goToMaxValue() {t = 67527641.222}
function plus1s() {t += 100*speedMultiplier}
function minus1s() {t = Math.max(0, t - 100*speedMultiplier)}
function plus1min() {t += 6000*speedMultiplier}
function minus1min() {t = Math.max(0, t - 6000*speedMultiplier)}
function plus1h() {t += 360000*speedMultiplier}
function minus1h() {t = Math.max(0, t - 360000*speedMultiplier)}
function speedDouble() {document.getElementById("speedMult").value *= 2}
function speedHalf() {document.getElementById("speedMult").value /= 2}
function reverseSpeed() {document.getElementById("speedMult").value *= -1}
function showNumber() {
	showNum = !(showNum);
}
function displayOutput() {
	document.getElementById("number").innerHTML = valueNumber(t);
	if (showNum) {
	document.getElementById("number2").innerHTML = valueNumber2(t) + "<br>";
	} else {
	document.getElementById("number2").innerHTML = "";
	}
	t = Math.min(Math.max(t+tAdder*speedMultiplier, 0),67527641.222);
	if (isNaN(document.getElementById("speedMult").value)) {
		speedMultiplier = 0;
		} else {
		speedMultiplier = document.getElementById("speedMult").value;
		}
	}
load();
setInterval(displayOutput, runInterval);
setInterval(save, runInterval);

</script>
</body>
</html>
