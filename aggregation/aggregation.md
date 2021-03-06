# Simple example
## db schema
{
	"_id" : ObjectId("50b1aa983b3d0043b51b2c52"),
	"name" : "Nexus 7",
	"category" : "Tablets",
	"manufacturer" : "Google",
	"price" : 199
}
## query
db.products.aggregate([
	{
		$group: {
			_id: "$manufacturer",
			num_products: {
				$sum: 1
			}
		}
	}
])

## counting using $sum
## schema
{
	"city" : "ACMAR",
	"loc" : [
		-86.51557,
		33.584132
	],
	"pop" : 6055,
	"state" : "AL",
	"_id" : "35004"
}
## query
db.zips.aggregate([
	{
		$group: {
			_id: "$state",
			population: {
				$sum: "$pop"
			}
		}
	}
])

## getting average data
same schema as before

## query
db.zips.aggregate([
	{
		$group: {
			_id: "$state",
			average_pop: { $avg: "$pop" }
		}
	}
])

## creating new array of of aggregated data using $addToSet
same schema

## query
db.zips.aggregate([{
	$group: {
		_id: "$city",
		postal_codes: { $addToSet: "$_id" }
	}
}])

## using $push - difference is that $push does not care if the same name is in the array

## $max and $min
## query
db.zips.aggregate([{
	$group: {
		_id: "$state",
		pop: {
			$max: "$pop"
		}
	}
}])

## using $match to filter out documents
same schema

## query
db.zips.aggregate([
	{
		$match: {
			state: 'NY'
		}
	}
])

### $match can filter like regular find
this will filter out documents that have less than 100000 population

db.zips.aggregate([
	{
		$match: {
			pop: {
				$gt: 100000
			}
		}
	}
])

## $sort - after projection
same schema

## query - sort by state, city both ascending
db.zips.aggregate([{ $sort: { state: 1, city: 1 } }])

same schema, using the $match

db.zips.aggregate([
	{
		$match: {
			state: 'NY'
		}
	},
	{
		$group: {
			_id: '$city',
			population: {
				$sum: '$pop'
			},
			zips: {
				$addToSet: '$_id'
			}
		}
	},
	{
		$project: {
			_id: 0,
			city: '$_id',
			population: 1,
			zips: 1
		}
	},
	{
		$sort: {
			population: -1
		}
	}
])

## $skip and $limit // !important - always use $skip first then $limit and only
// after you $sort

## query - this will skip the first 5 results and limit the rest to 3
db.zips.aggregate([
	{
		$match: {
			state: 'NY'
		}
	},
	{
		$group: {
			_id: '$city',
			population: {
				$sum: '$pop'
			},
			zips: {
				$addToSet: '$_id'
			}
		}
	},
	{
		$project: {
			_id: 0,
			city: '$_id',
			population: 1,
			zips: 1
		}
	},
	{
		$sort: {
			population: -1
		}
	},
	{
		$skip: 5
	},
	{
		$limit: 3
	}
])

## using $first and $last
same schema

## query to sort by state and city and show only largest city in each state
db.zips.aggregate([
	{
		$group: {
			_id: {
				state: "$state",
				city: "$city"
			},
			population: {
				$sum: "$pop"
			}
		}
	}
])












