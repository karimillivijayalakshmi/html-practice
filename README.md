
//main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
 const port =  await app.listen(3000);
  //console.log("port listening..",port)
}
bootstrap();
//appservices
import { Injectable } from "@nestjs/common";

@Injectable()
export class AppService {
  getHello(): string {
    return 'Hello World!';
  }
}
//app.modules
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { BookModule } from './books/books.module';
import { OrderModule } from './order/order.module';
import { UserModule } from './users/users.module';
@Module({
  imports: [
    MongooseModule.forRoot('mongodb://127.0.0.1:27017/books', {
      connectionName: "Books",
      connectionFactory: (connection) => {
        console.log("data base connected")
        return connection;
      }
    }),BookModule,OrderModule,UserModule],
  controllers: [AppController],
  providers: [AppService],
  exports: [MongooseModule]
})
export class AppModule { }
//appcontroller
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller("/v1")
export class AppController {
  constructor(private readonly appService: AppService) { }
  @Get()
  getHello(): string {
    return this.appService.getHello();
  }
}
user controller module service
import { Controller, Delete, Get, Param, Post, Put, Patch, Req ,Query } from '@nestjs/common';
import { UserService } from './user.services';

@Controller("/v1")
export class UserController {
  constructor(private readonly userService: UserService) { }

@Get("user")
  async getuser(): Promise<any> {
    return await this.userService.getuser();
  }
  @Get("user/count")
  async getuserCount(): Promise<any> {
    return await this.userService.getuserCount();
  }
  @Post("user")
  async postuser(@Req() req: any): Promise<any> {
    return await this.userService.postuser(req); 
  }
  @Put("order/:id")
  async putuser(@Req() req: any, @Param("id") id:string): Promise<any> {
    return await this.userService.putuser(req, id);
  }
  @Delete("user/:id")
  async deleteuser(@Param("id") id:string): Promise<any> {
    return await this.userService.deleteuser(id);
  }
}
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { UserController } from './users.controller';
import { UserService } from './user.services';
import { userDetails, userSchema } from '../Schema/user.schema'


@Module({
  imports: [
    
    MongooseModule.forFeature([
      { name: userDetails.name, schema: userSchema } ], "Books")],
  controllers: [UserController],
  providers: [UserService],
  exports: [MongooseModule]
})
export class UserModule { }
import { Injectable } from '@nestjs/common';
import { userDetails } from '../Schema/user.schema';
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';
Injectable()
export class UserService {
constructor(@InjectModel(userDetails.name, "Books") private userDet: Model<userDetails>) {}
async getuser(): Promise<any> {
    let findata =  await this.userDet.find()
    return findata;
  }
  async getuserCount(): Promise<any> {
    let findata =  await this.userDet.find().count()
    return findata;
  }
  async postuser(req: any): Promise<any> {
    let dataCreate =  await this.userDet.create(req.body)
    return dataCreate;
  }
  async putuser(req: any, id: string): Promise<any> {
    let body = req.body;
    let dataCreate =  await this.userDet.findOneAndUpdate({_id: id}, {$set: body}, {new: true})
    return dataCreate;
  }
  async deleteuser(id: string): Promise<any> {
    await this.userDet.deleteOne({_id: id})
    return "Deleted Successfully";
  }
}
books

import { Controller, Delete, Get, Param, Post, Put, Req ,Query } from '@nestjs/common';
import { BookService } from './books.service';
import { Observable } from 'rxjs';

@Controller("/v1")
export class BookController {
  constructor(private readonly bookService: BookService) { }


@Get("book")
  async getbook(): Promise<object> {
    return await this.bookService.getbook();
  }
  @Get("book/count")
  async getbookCount(): Promise<object> {
    return await this.bookService.getbookCount();
  }
  @Get("book/title")
  async getBookByTitle(@Query() data:{title?:string}): Promise<object> {
   return await this.bookService.gbbTitle(data.title);
  }
  @Get("book/author")
  async getaBookByAuthor(@Query() data:any): Promise<any> {
   return await this.bookService.gbbAuth(data.author);
  }
  @Get("book/pages")
  async getaBookByPages(@Query() data:any): Promise<object> {
   return await this.bookService.gbbPages(data.pages);
  }
  @Get("book/category")
  async getaBookByCategory(@Query() data:any): Promise<any>{
  return await this.bookService.gbbCat(data.category);}

  @Get("book/price")
  async getaBookByPrice(@Query() data:any): Promise<any> {
   return await this.bookService.gbbPrice(data.price);
  }
  @Get("book/isAvail")
  async getaBookByavail(@Query() data:{isAvail?:string}): Promise<string>{
   return await this.bookService.gbbAvail(data.isAvail);
  }
  
  
  @Get("book/:id")
  async getBook(@Param("id") id:string): Promise<any> {
    return await this.bookService.getBook(id);
  }
  @Post("book")
  async postBook(@Req() req: any): Promise<any> {
    return await this.bookService.postBook(req); 
  }
 
  @Put("book/:id")
  async putBook(@Req() req: any, @Param("id") id:string): Promise<any> {
    return await this.bookService.putBook(req, id);
  }
  
  @Delete("book/:id")
  async deleteBook(@Param("id") id:string): Promise<string> {
    return await this.bookService.deleteBook(id);
  }}
  import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { BookController } from './books.controller';
import { BookService } from './books.service';
import { booksDetails, booksSchema } from '../Schema/book.schemas'


@Module({
  imports: [
    
    MongooseModule.forFeature([
      { name: booksDetails.name, schema: booksSchema } ], "Books")],
  controllers: [BookController],
  providers: [BookService],
  exports: [MongooseModule]
})
export class BookModule { }
import { Injectable } from '@nestjs/common';
import { booksDetails } from '../Schema/book.schemas';
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';

@Injectable()
export class BookService {
  constructor(@InjectModel(booksDetails.name, "Books") private bookDet: Model<booksDetails>) {}
  async getbook(): Promise<any> {
    let findata =  await this.bookDet.find()
    return findata;
  }
  async getbookCount(): Promise<any> {
    let findata =  await this.bookDet.find().count()
    return findata;
  }
  async getBook(id: string): Promise<any> {
    let findata =  await this.bookDet.findOne({_id: id})
    return findata;
  }
  async postBook(req: any): Promise<any> {
    let body = req.body;
   
    let dataCreate =  await this.bookDet.create(body)
    return dataCreate;
  }
  async gbbTitle(name: string): Promise<any> {
    let findata =  await this.bookDet.findOne({title: name})
    return findata;
  }
  async gbbAuth(writer: string): Promise<any> {
    let findata =  await this.bookDet.findOne({author: writer})
    return findata;
  }

  async gbbPages(pages :number){
    let findata =  await this.bookDet.findOne({pages: pages})
    return findata;
  }
  async gbbPrice(price :number){
        let findata =  await this.bookDet.findOne({price: price})
        return findata;}
        

    async gbbCat(cat: string): Promise<any> {
      let findata =  await this.bookDet.findOne({category: cat})
      return findata;
  }
  async gbbAvail(avail: string): Promise<any> {
    let findata =  await this.bookDet.find({isAvail: avail})
    return findata;
}
async deleteBook(id: string): Promise<any> {
    await this.bookDet.deleteOne({_id: id})
    return "Deleted Successfully";
  }
  async putBook(req: any, id: string): Promise<any> {
    let body = req.body;
    let dataCreate =  await this.bookDet.findOneAndUpdate({_id: id}, {$set: body}, {new: true})
    return dataCreate;
  }
}
order
import { Controller, Delete, Get, Param, Post, Put, Patch, Req ,Query } from '@nestjs/common';
import { OrderService } from './order.service';

@Controller("/v1")
export class OrderController {
  constructor(private readonly orderService: OrderService) { }
  @Get("order")
  async getorder(): Promise<object> {
    return await this.orderService.getorder();
  }
  @Get("order/count")
  async getorderCount(): Promise<number> {
    return await this.orderService.getorderCount();
  }
  
  @Get("order/username")
  async getUserByName(@Query() data:any): Promise<object> {
   return await this.orderService.gobuName(data.userName);
  }
  @Get("order/email")
  async getUserByEmail(@Query() data:any): Promise<any> {
   return await this.orderService.gobuEmail(data.email);
  }
  @Get("order/phonenumber")
  async getUserByPhoneNumber(@Query() data:any): Promise<any> {
   return await this.orderService.gobuPhone(data.phoneNumber);
  }

  @Get("order/:id")
  async getOrders(@Param() id:any): Promise<object> {
    console.log("get object id...",id.id)
    return await this.orderService.getOrder(id);
  }
  
  @Post("order")
  async postOrder(@Req() req: any): Promise<any> {
    if(req.body.quantity != 0){
    let id = req.body.bookid ;let quant = req.body.quantity;let name =req.body.userName;let uId =req.body.userid;
    return await this.orderService.postOrder(req,id,quant,name,uId);}
    else return "order cant be placed"
  }

  @Put("order/:id")
  async putOrder(@Req() req: any, @Param("id") id:string): Promise<object> {
    return await this.orderService.putOrder(req, id);
  }

  @Delete("order/:id")
  async deleteOrder(@Param("id") id:string): Promise<any> {
    return await this.orderService.deleteOrder(id);
  }}
  import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { OrderController } from './order.controller';
import { OrderService } from './order.service';
import { orderDetails, orderSchema } from '../Schema/orders.schema'
import { booksDetails, booksSchema } from 'src/Schema/book.schemas';
import { userDetails, userSchema } from 'src/Schema/user.schema';


@Module({
  imports: [
    
    MongooseModule.forFeature([
      { name: orderDetails.name, schema: orderSchema },
      { name: booksDetails.name, schema: booksSchema },
      { name: userDetails.name, schema: userSchema } ], "Books")],
  controllers: [OrderController],
  providers: [OrderService],
  exports: [MongooseModule]
})
export class OrderModule { }
import { Injectable } from '@nestjs/common';
import { orderDetails } from '../Schema/orders.schema';
import { InjectModel } from '@nestjs/mongoose';
import mongoose, { Model } from 'mongoose';
// import { BookModule } from 'src/books/books.module';
// import { UserModule } from 'src/users/users.module';
import { booksDetails } from 'src/Schema/book.schemas';
import { userDetails } from 'src/Schema/user.schema';
@Injectable()
export class OrderService {
  constructor(@InjectModel(orderDetails.name, "Books") private orderDet: Model<orderDetails>,
    @InjectModel(booksDetails.name, "Books") private bookDet: Model<booksDetails>,
    @InjectModel(userDetails.name, "Books") private userDet: Model<userDetails>) { }
  async getorder(): Promise<any> {
    let findata = await this.orderDet.find()
    return findata;
  }
  async getorderCount(): Promise<number> {
    let findata = await this.orderDet.find().count()
    return findata;
  }
  async getOrder(id: any): Promise<object> {
    //  let foundData= this.orderDet.aggregate([
    //   {$match: {_id : new mongoose.Types.ObjectId(id)}},
    //    {
    //     $lookup: {
    //       from: "booksdetails",
    //       localField: "bookid",
    //       foreignField: "_id",
    //       as: "bookDetails"
    //     }
    //   },
    //   {
    //     $lookup: {
    //       from: "userdetails",
    //       localField: "userid",
    //       foreignField: "_id",
    //       as: "userdetail"
    //     }
    //   }
    // ])

    let findata = await this.orderDet.findOne({ _id: new mongoose.Types.ObjectId(id)}).populate(["bookid","userid"])
    return findata;
  }

  async gobuName(Username: string): Promise<any> {
    let findata = await this.orderDet.findOne({ userName: Username })
    return findata;
  }
  async gobuEmail(email: string): Promise<any> {
    let findata = await this.orderDet.findOne({ email: email })
    return findata;
  }
  async gobuPhone(phone: number): Promise<any> {
    let findata = await this.orderDet.findOne({ phoneNumber: phone })
    return findata;
  }
  async postOrder(req: any, id: number, quant: number, name: string, uId: string): Promise<any> {
    let valid = await this.userDet.findOne({ userName: name, _id: uId })
    if (valid == null) { return "order cant be placed" }
    else {
      let body = req.body;
      let datacreate = await this.bookDet.findOne({ _id: id, quantity: { $gte: quant } })
      if (datacreate != null) {

        let orderObj = await this.orderDet.create(body);
        const booksDetails = await this.bookDet.findOneAndUpdate({ _id: id }, { '$inc': { 'quantity': -quant } }, { new: true }).exec()
        if (booksDetails.quantity == 0) {
          await this.bookDet.findOneAndUpdate({ _id: booksDetails._id }, { '$set': { isAvail: "No" } }, { new: true }).exec()
        }
        return orderObj;
      }
      else {
        return "order cant be placed"
      }
    }
  }
  async putOrder(req: any, id: string): Promise<object> {
    let body = req.body;
    let dataCreate = await this.orderDet.findOneAndUpdate({ _id: id }, { $set: body }, { new: true })
    return dataCreate;
  }

  async deleteOrder(id: string): Promise<string> {
    await this.orderDet.deleteOne({ _id: id })
    return "Deleted Successfully";
  }
}
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
@Schema()
export class booksDetails{
  @Prop()
  title: String;

  @Prop()
  author: string;

  @Prop()
  pages: number;

  @Prop()
  category: string;

  @Prop()
  price: number;

  @Prop()
  published_year: number;

  @Prop()
  quantity: number;

  @Prop()
  isAvail: string;
}
export const booksSchema = SchemaFactory.createForClass(booksDetails);
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { ObjectId } from 'mongodb';
import mongoose from 'mongoose';
@Schema()
export class orderDetails{
  @Prop()
  userName: string;

  @Prop()
  email: string;

  @Prop()
  phoneNumber: number;

  @Prop({type: ObjectId, ref: "booksDetails"})
  bookid: ObjectId;

  @Prop()
  orderdate: string;

  @Prop()
  address: string;

  @Prop()
  quantity: number;

  @Prop({type: ObjectId, ref: "userDetails"})
  userid: string;
}

export const orderSchema = SchemaFactory.createForClass(orderDetails);
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
@Schema()
export class userDetails{
    @Prop()
    userName:string;
}
export const userSchema = SchemaFactory.createForClass(userDetails);






  

  


